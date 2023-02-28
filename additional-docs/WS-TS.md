Web Server - Technology Stack
==============
**Author:** *Benjamin Whelan* 

*Documentation explaining the backend technology stack, including why I **chose certain** **languages**, **frameworks** and coding **implementations**.*

# Server-side web appllication serving a complete web service.
## Requirements (for `web-server`)
- Make use of the kernels asynchronous read and write API through epoll/select/poll
- Be able to offload long-running tasks in the user space and have some form of mechanism to notify us when the task is done so we can progress with our work. 
  - This is a runtime creating and managing the green threads.
- A syntax within the programming language so we can mark asynchronous blocks in our code such that the compiler understands what to do with them.
- A specific type in the standard library for mutually exclusive access and modification. 
  - Instead of a type like number, where it stores a specific value, a type for asynchronous programming needs to store, next to the value, the current state of the long-running operation.

## Runtime
- responsible for:
    - creating threads
    - polling the `Future`s
    - driving the `Future`s to completion
    - passing on work to kernel

![The complete asynchronous Rust environment.](https://raw.githubusercontent.com/wiki/dainank/know-nav-web-server-api/assets/images/complete-async-rust-env.jpg?token=GHSAT0AAAAAAB54BVHGUSN3RPL5LKWU4QZQY75467Q)
    
![Executing async HTTP requests behind the scenes.](https://raw.githubusercontent.com/wiki/dainank/know-nav-web-server-api/assets/images/exe-async-http-request.jpg?token=GHSAT0AAAAAAB54BVHH42ITH6UW4CBCNGYGY75474Q)

1. The runtime takes this piece of code and hands it over to the Executor. The Executor is responsible for calling the poll method on the Future.
2. If it’s a network request, the runtime is handing it over to mio, which creates the asynchronous socket in the kernel, and requests some CPU time to finish the task.
3. Once the kernel is done with the work (like sending out a request and getting a response), it is notifying the waiting process on the socket.
4. The Reactor is then responsible for waking up Executor, which continues with the computation with the results returned from the kernel.
    

## Notes
For its syntax, Rust offers a combination of async and await as keywords. You mark a function `async` so you can use `await` inside of it. A function which you `await` returns a `Future` type, which has the type of value you return in case of a successful execution, and a method called poll which executes the long-running process and yields back `Pending` or `Ready`. The Ready state can then either have an `Error` or a successful return value.

> most runtime libraries already abstract over the kernal API (usually using another library called `mio`)

### Web Frameworks for Rust
- `actix` is the more complete, actively used web framework, and packs a lot of features. It can be opinionated at times.
- `rocket` uses macros to annotate route handlers, and has JSON parsing built in. It is a very complete framework with all the features included to write solid web servers.
- `warp` was one of the first web frameworks for Rust. It is developed close to the `tokio` community and offers a lot of freedom. It is the most bare-bone framework which leaves many design decisions to the developer.
- `axum` is the newest of the bunch and tries to build as much on top of already existing crates from the tokio ecosystem, and with design lessons learned from warp and others.

> `actix` brings its own runtime (but you can also choose to use tokio). The frameworks rocket, warp and axum are using tokio.

A brief overview follows of where we use what:
![When using warp, you inherit the runtime tokio and hyper as the HTTP abstraction and server under the hood.](https://raw.githubusercontent.com/wiki/dainank/know-nav-web-server-api/assets/images/when-using-what.jpg?token=GHSAT0AAAAAAB54BVHGK4VQ5K5DITMY2EMUY755ADQ)

### Warp Framework
#### Context
- minimal, maintained with an active community around it
- based on de facto standard `Tokio` runtime
- well documented and open-source on GitHub

#### Included with Warp
- *Hyper*, a HTTP server 
- *Tokio*, a runtime

#### Warp's Filter System
Routes are actually a set of filters, chained together. Each request attempts to match a filter created; if none found, it moves to the next one:
```rust
// import the Filter trait from warp
use warp::Filter;
#[tokio::main]
async fn main() {
   // create a path Filter
   let hello = warp::path("hello").map(|| format!("Hello, World!"));

   // start the server and pass the route filter to it
   warp::serve(hello).run(([127, 0, 0, 1], 3030)).await;
}
```

### HTTP Request Processing
1. Check the request path inside the HTTP request.
2. Check the HTTP method (for example, GET, PUT, or POST).
3. Forward the request to a route handler that is responsible for the path and type.
4. Before forwarding the request to the route handler, the request can be passed through middleware, which checks things like authentication headers or adds further information to the request that is handed down to the route handler.

![Workflow of sending, parsing, and answering HTTP GET requests with Warp.](https://raw.githubusercontent.com/wiki/dainank/know-nav-web-server-api/assets/images/http-workflow.jpg?token=GHSAT0AAAAAAB54BVHGLRD3F5BTFEH6JEHAY755ALA)

Consider the following checklist a great starting point for understanding a web framework:
- How does it parse the incoming `PATH` and HTTP method?
- Can I parse JSON requests directly from the HTTP body?
- How can I parse uniform resource identifier (URI) parameters from the request?
- How can I add middleware such as authentication or logging?
- How do I pass objects like a database connection to the route handlers?
- How do I have to return an HTTP response?
- Does it have a built-in session or cookie handling?

#### Responsibilities of a Route Handler
A Route Handler must return:
- A result
- `warp::Reply` for the success part
- `warp::Rejection` for the error

`Serde` is the standard library for serialization (and deserialization)