# Database
> Database design choices and integration with Rust.

See: https://docs.rs/postgres/0.16.0-rc.2/postgres/types/trait.FromSql.html

There are several questions that must be answered before deciding and implementing a database in a project:
- Do you want an object-relational mapping (ORM) tool or do you want to write Structured Query Language (SQL) commands directly?
- Should this crate work in a synchronous or asynchronous way?
- Do you have the need for migration scripts, and does the crate you chose support it?
- Do you need additional safety around your queries (type checking)?
- Do you need connection pooling, support for transactions, and batch processing?

I have opted to use **[PostgreSQL](https://www.postgresql.org/)**, directly with `SQL` commands ratehr than an `ORM` since this keeps the code cleaner (separating database logic from the code).

Reasons why [`SQLx`](https://github.com/launchbadge/sqlx) is a great choice for `Rust` projects:
- It is asynchronous.
- The PostgreSQL driver is written in Rust.
- It supports multiple database engines (MySQL, PostgreSQL, SQLite).
- It works with different runtimes (Tokio, async-std, Actix Web).
- It is widely used in the community.

## Connection Pools
> more than one database connection open simultaneously

Since we work in async environments, we will **have more than one database query running at the same time**. We can open a **fixed number of connections** and a dependency can manage which requests go where. Additionally, we can **limit the number of connections**, thus control the server and **not let it get overwhelmed**.