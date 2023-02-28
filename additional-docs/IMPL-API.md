# Initializing Data
The core philosophy of our implementation is to have a **store** that we can return, remove from, add to and alter from rather than creating data on demand and returning it. We use a hashmap for this store to assign data to IDs, thus do not need to iterate over a list of questions, every time we want a specific one.

```rs
use std::collections::HashMap;

struct Store {
   questions: HashMap<QuestionId, Question>,
}
```

We can implement these methods on our store with the following responsibilities:
* `new` - custom constructor
* `init` - initialize store object with JSON file or database further during development
* `add_data` - using `insert` we can add data to our existing data in the store

Clone is required to transfer ownership of the `id` string to our store.
```rs
fn add_question(mut self, question: Question) -> Self {
        self.questions.insert(question.id.clone(), question);
        self        
    }
```

_Essentially, all the data can be initialized from the start and nothing needs to be added manually to our store._

# Reading Data
Each HTTP request that our backend receives will run through a set of filters, modifying the data along the way. Our filter will receive our store and pass it to each route we want access to it. Some details on the various filter sections:
* `warp::any` - match any, thus all requests
* `map` - passes value to receiving function
* `move` - simply passes value & ownership of values into closure
* `clone` - we return clone since every function accessing store needs access to it (this is because we will have multiple route handlers accessing store)
* `values` - discord hash map keys and clone hash map values (since `collect` requires ownership over values, not just reference)

```rs
let get_data = warp::get()
        .and(warp::path("data"))
        .and(warp::path::end())
        .and(store_filter)
        .and_then(get_data)
        .recover(return_error);
```

## Parsing Query Parameters
An additional filter can handle requests with querries:
```rs
localhost:3030/questions?start=201&end=400

.and(warp::query())
```

## Returning Custom Errors
We can create an enum to cover all error cases for our API:
```rs
#[derive(Debug)]
enum Error {
    ParseError(std::num::ParseIntError),
    MissingParameters,
    QuestionNotFound,
}

impl std::fmt::Display for Error {
    fn fmt(&self, f: &mut std::fmt::Formatter) -> std::fmt::Result {
        match *self {
            Error::ParseError(ref err) => write!(f, "Cannot parse parameter: {}", err),
            Error::MissingParameters => write!(f, "Missing parameter"),
            Error::QuestionNotFound => write!(f, "Question not found"),
        }
    }
}
```

## Modifying Data
### Arc
Thousands or more requests can come in at the same second, with every needing to write or read data. Store access must be given to each request separately, notifying future requests to wait until the previous request is done with `Store` (essentially a waiting list). This causes problems with the asynchronous mindset required with web servers:
* Preventing two or more processes from altering data at the same time
* Giving each route handler ownership over the data store if needed so it can be mutated

Rust transfers ownership when passing values around in the code. Complex data structures will be marked with `uninit`, making sure one pointer on the stack has ownership over this structure found on the heap (one pointer can therefore alter it). There are two solutions for this problem:
* `Rc<T>` - single-threaded systems
* `Arc<T>` - multi-threaded systems (share data among multiple threads)

A pointer on stack refers to complex data structure on heap as before, but a copy is made of the pointer that still references the same data:
```rust
let x = Arc::new(String::from("hello")); // 1
let y = Arc::clone(&x); // 2
```
1. Same as before, pointer on stack to structure on heap.
2. Copied pointer that references same data on heap. The counter of the `Arc` is incremented by one.

Whenever any of the variables (pointers) go out of scope, the counter will decrement. If the counter reaches 0, the data structure itself is removed from heap. Some key points about `Arc` (*atomically reference counter*):
* wrapped data is moved into heap; creates pointer to it on the stack
* cloning `Arc`, clones the pointer, pointing to the same data structure on heap with internally `Arc` incrementing its counter.

`Tokio` is multithreaded, thus we need to use `Arc`.

### RwLock
We also need to find solutions for creating and altering existing data. Both provide write access to underlying data, locking it when in use and unlocking it for next reader/writer when complete:
* `Mutex` - blocks for both reader/writer
* `RwLock` - blocks only writer (thus allows many writers)

We can now wrap our data in the store as follows:
```rs
questions: Arc<RwLock<HashMap<QuestionId, Question>>>
```
`Arc` **allows multiple pointers** to it while `RwLock` **prevents multiple writes** at same time. Now we can get the data:
* `read` - Locks this `RwLock` with shared read access, causing the current task to yield until the lock has been acquired.
  * hence why we need to `.await`

### Post Request Support
See implementation:
```rust
store
        .questions
        .write()
        .await
        .insert(question.id.clone(), question);
```
* `write` - Locks this RwLock with exclusive write access, causing the current task to yield until the lock has been acquired.
  * hence why we need to `.await`
* `insert` - simple insert into HashMap

### Put Request Support
1. Request `mut` reference to question we are accessing
2. We can overwrite the found question with `*q = question`:
```rust
match store.questions.write().await.get_mut(&QuestionId(id)) {
    Some(q) => *q = question,
    None => return Err(warp::reject::custom(Error::QuestionNotFound)),
}
```

### Delete Request Support
See implementation:
```rust
match store.questions.write().await.remove(&QuestionId(id)) {
    Some(_) => return Ok(warp::reply::with_status("Question deleted", StatusCode::OK)),
    None => return Err(warp::reject::custom(Error::QuestionNotFound)),
}
```
The underscore `_` simply signals that we are not interesting nor need the return value from the match block, we only want to see if **something** returned.