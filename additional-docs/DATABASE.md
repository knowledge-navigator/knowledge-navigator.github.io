# Database
> Database design choices and integration with Rust.

There are several questions that must be answered before deciding and implementing a database in a project:
- Do you want an object-relational mapping (ORM) tool or do you want to write Structured Query Language (SQL) commands directly?
- Should this crate work in a synchronous or asynchronous way?
- Do you have the need for migration scripts, and does the crate you chose support it?
- Do you need additional safety around your queries (type checking)?
- Do you need connection pooling, support for transactions, and batch processing?

I have opted to use **[PostgreSQL](https://www.postgresql.org/)**