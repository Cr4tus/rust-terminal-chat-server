# Rust Terminal Chat Server

## What does the app do?
The server will run an infinite loop that accepts clients and send any message successfully received from a client to all other clients via TCP connection.

## Programming Languages:
- Rust

## Frameworks & Libraries:
There is no need to add any other package

## Implementation:
First, we define the *constants* and the *utils* module:
```rust
mod constants {
    pub const LOCAL_HOST: &str = "127.0.0.1:6000";
    pub const MAX_MSG_LENGTH: usize = 32;
    pub const DEFAULT_THREAD_SLEEP_MILLIS: u64 = 100;
}

mod  utils {
    pub fn sleep_for(millis: u64) {
        std::thread::sleep(std::time::Duration::from_millis(millis));
    }
}
```
The most important parts are the 2 big *if* blocks within the main loop from the *main* function. The first one is creating a thread for each client and the second one receives the messages and send them to the others:
```rust
if let Ok(message) = receiver.try_recv() {
    client_tcp_streams = client_tcp_streams.into_iter().filter_map(
        |mut client_tcp_stream| {
            let mut buffer = message.clone().into_bytes();
            buffer.resize(constants::MAX_MSG_LENGTH, 0);

            client_tcp_stream
                .write_all(&buffer)
                .map(|_| client_tcp_stream)
                .ok()
        }
    ).collect::<Vec<_>>();
}
```