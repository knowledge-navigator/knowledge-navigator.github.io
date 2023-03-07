Please note that I opted to use **Tracing** over **Logging**. Scroll further down for tracing information (logging provides good context for tracing).

# Logging
> Standard practices for logging as well as debugging an application and how this is applied to my personal project.

Some key responsibilities of logging include:
- Logging should have more information and a fixed structure.
- println! uses stdout as an output stream in your application.
- Logging generally sends output to stderr (a standard adopted by libraries, although you can log to stdout as well).
- You can choose to log to the terminal, a file, or a server.
- Logging has different levels (from info to critical).
- If you target machines with your logs (to process log information, for example), using JSON as your logging format is preferred.
- If you mostly use your logs for humans to consume, standard text separated by commas or spaces is the way to go.

Loging levels include:
- `debug` — Used for debugging during development
- `info` — Information purposes only
- `warning` — Indicates not mission-critical problems
- `error` — A typical error such as a closed database connection
- `trace` — Used in one-off debugging or builds, indicates fine-grained logging
- `critical` — Indicates mission-critical errors that should be addressed immediately

For example:
```rs
use log::{info, warn, error, debug};

info!("User {} logged in", user.id);
warn!("User {} logged in {} times", user.id, login_count);
err!("Failed to load User {} from DB", user.id);
debug!(
       "User {} access controls: {}, {}", 
       user.id, user.admin, user.supervisor
);
```

# Tracing
The following concepts solve many of the logging issues related to large enterprise software.

TLDR: A span is a moment in time, in which events can happen. Each event is then collected by a subscriber, which will store all the events and, depending on the settings, send them to a file or `stdout`, in the format you specify (JSON, for example).

## Spans
> A period with a start (request) and an end (response).

The `instrument!` macro, opens and closes spans, thus annotating any async functions with this macro will apply this.

## Events
> The logs themselves taking place within spans.

These can be labelled with the `event!` macro.

## Subscribers
> Responsible for logging these events to console, in our case with an `fmt` subscriber.