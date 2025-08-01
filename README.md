<div align="center">
  <img src="docs/images/rustycord-logo.png" alt="rustycord Logo" width="128" height="128">
  
  # rustycord 🦀

[![Crates.io](https://img.shields.io/crates/v/rustycord.svg)](https://crates.io/crates/rustycord) [![Documentation](https://docs.rs/rustycord/badge.svg)](https://docs.rs/rustycord) [![dependency status](https://deps.rs/repo/github/iamdhakrey/rustycord/status.svg)](https://deps.rs/repo/github/iamdhakrey/rustycord) [![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE) [![GitHub Stars](https://img.shields.io/github/stars/iamdhakrey/rustycord.svg)](https://github.com/iamdhakrey/rustycord/stargazers)   [![GitHub Issues](https://img.shields.io/github/issues/iamdhakrey/rustycord.svg)](https://github.com/iamdhakrey/rustycord/issues)   [![GitHub Pull Requests](https://img.shields.io/github/issues-pr/iamdhakrey/rustycord.svg)](https://github.com/iamdhakrey/rustycord/pulls)   [![Rust](https://img.shields.io/badge/rust-1.60%2B-orange.svg)](https://www.rust-lang.org)   [![Discord API](https://img.shields.io/badge/discord%20api-v10-7289da.svg)](https://discord.com/developers/docs/)
</div>

> **⚠️ DEVELOPMENT NOTICE**: rustycord is currently in **heavy development** and is **NOT ready for production use**. APIs may change frequently, features are incomplete, and breaking changes occur regularly. **Do not use for production bots yet.** Wait for the stable 1.0 release.

A modern, fast, and easy-to-use Discord bot library for Rust, designed with a focus on simplicity and developer experience.

## 🚧 Current Development Status

- ✅ **Basic bot functionality** - Working
- ✅ **Message handling system** - Functional
- ✅ **Prefix command system** - Implemented
- ✅ **HTTP client** - Basic functionality
- ✅ **Gateway connection** - Stable
- ⚠️ **Advanced features** - Under development
- ❌ **Slash commands** - Not implemented
- ❌ **Voice support** - Not available
- ❌ **Comprehensive testing** - Needed
- ❌ **Stable API** - Still changing

**Use only for experimentation and learning. Not suitable for production bots.**

## Features ✨

- **Simple API**: Easy-to-use message handling system
- **Fast**: Built with async/await and tokio for high performance
- **Flexible**: Modular architecture with customizable handlers
- **Well-typed**: Full type safety with serde serialization
- **Event-driven**: Comprehensive event system for Discord Gateway
- **Shard support**: Built-in sharding for large bots
- **Modern**: Uses latest Rust features and best practices
- **Rich Logging**: Comprehensive logging system with multiple levels and colored output
- **Developer-Friendly**: Extensive debug information and easy troubleshooting

## Quick Start 🚀

### Basic Bot

```rust
use rustycord::{bot::Bot, gateway::intents};

#[tokio::main]
async fn main() {
    let token = "YOUR_BOT_TOKEN".to_string();
    let intent = intents::ALL_INTENTS;
    
    // Start bot with debug logging to see what's happening
    Bot::builder(Some(intent))
        .await
        .run(token, Some("debug".to_string()))
        .await;
}
```

### Logging Configuration

rustycord provides comprehensive logging to help you understand what your bot is doing:

```rust
use rustycord::logger::setup_logger;

#[tokio::main]
async fn main() {
    // Set up logging manually with your preferred level:
    // "trace" - Most detailed (includes message contents)  
    // "debug" - Detailed operational information
    // "info"  - Important events only (recommended for production)
    // "warn"  - Warnings and errors only
    // "error" - Errors only
    
    setup_logger("debug".to_string()).expect("Failed to initialize logger");
    
    // Your bot code here...
}
```

Example log output:
```
2024-07-17 15:30:45 :: [INFO] :: 🚀 Starting bot...
2024-07-17 15:30:45 :: [DEBUG] :: 🌐 Making request to Discord API
2024-07-17 15:30:45 :: [INFO] :: ✅ Successfully authenticated as: MyBot#1234
2024-07-17 15:30:45 :: [INFO] :: 🔌 Connected to The Discord
2024-07-17 15:30:45 :: [INFO] :: 🚀 Bot is ready!
2024-07-17 15:30:50 :: [INFO] :: 📨 Message received: Hello! from Username
2024-07-17 15:30:50 :: [DEBUG] :: 🔄 Processing MESSAGE_CREATE with 2 handler(s)
```

### Message Handler

```rust
use rustycord::{
    bot::Bot,
    gateway::intents,
    handlers::message_handler::{MessageHandler, MessageHandlerResult},
    message::ChannelMessage,
    client::Client,
};
use async_trait::async_trait;

struct MyHandler;

#[async_trait]
impl MessageHandler for MyHandler {
    async fn on_message_create(&self, message: &ChannelMessage, client: &Client) -> MessageHandlerResult {
        // Ignore bot messages
        if message.author.bot.unwrap_or(false) {
            return Ok(());
        }
        
        // Respond to ping
        if message.content == "!ping" {
            client.send_text_message(&message.channel_id, "Pong! 🏓").await?;
        }
        
        Ok(())
    }
}

#[tokio::main]
async fn main() {
    let token = "YOUR_BOT_TOKEN".to_string();
    let intent = intents::ALL_INTENTS;
    let mut bot = Bot::builder(Some(intent)).await;
    
    // Register message handler
    if let Some(client) = &bot.client {
        let handlers = client.get_event_dispatcher().get_message_handlers();
        handlers.add_handler(MyHandler).await;
    }
    
    bot.run(token, Some("info".to_string())).await;
}
```

### Built-in Handlers

```rust
use rustycord::handlers::message_handler::{PingPongHandler, EchoMessageHandler};

// Add built-in handlers
handlers.add_handler(PingPongHandler).await;  // Responds to "ping" with "Pong!"
handlers.add_handler(EchoMessageHandler).await;  // Echoes back all messages
```

### Send Messages with Embeds

```rust
use rustycord::embeds::Embed;

let embed = Embed::new()
    .title("Hello World")
    .description("This is an embed!")
    .color(0x00ff00);

client.send_embed_message(&channel_id, vec![embed]).await?;
```

## API Overview 📚

### Core Components

- **Bot**: Main entry point for creating and running bots
- **Client**: HTTP client for Discord API interactions
- **MessageHandler**: Trait for handling message events
- **EventDispatcher**: Routes gateway events to handlers
- **Embed**: Rich embed message builder

### Message Handling

```rust
#[async_trait]
impl MessageHandler for MyHandler {
    async fn on_message_create(&self, message: &ChannelMessage, client: &Client) -> MessageHandlerResult {
        // Handle message creation
        Ok(())
    }
    
    async fn on_message_update(&self, message: &ChannelMessage, client: &Client) -> MessageHandlerResult {
        // Handle message updates (optional)
        Ok(())
    }
    
    async fn on_message_delete(&self, message_id: &str, channel_id: &str, client: &Client) -> MessageHandlerResult {
        // Handle message deletion (optional)
        Ok(())
    }
}
```

### Sending Messages

```rust
// Send text message
client.send_text_message(&channel_id, "Hello!").await?;

// Send message with embed
let embed = Embed::new().title("Title").description("Description");
client.send_embed_message(&channel_id, vec![embed]).await?;

// Send message with both text and embeds
client.send_message(&channel_id, "Text content", Some(vec![embed])).await?;
```


## Examples 📝

Check out the [examples](./examples) directory for more comprehensive examples:

- [Basic Bot](./examples/basic/) - Simple bot setup
- [Message Handler](./examples/message_handler/) - Advanced message handling

## Installation 📦

Add this to your `Cargo.toml`:

```toml
[dependencies]
rustycord = { git = "https://github.com/iamdhakrey/rustycord" }
tokio = { version = "1.0", features = ["full"] }
async-trait = "0.1"
```

## Development Status 🚧

rustycord is currently in active development. See [PROGRESS.md](./PROGRESS.md) for detailed status.

### Completed ✅
- Basic bot infrastructure
- Message handling system
- HTTP client for Discord API
- WebSocket gateway connection
- Event dispatching
- Embed support

### In Progress 🔄
- Command framework
- Slash commands
- Interaction handling
- Enhanced error handling

### Planned 📋
- Voice support
- Application commands
- Advanced caching
- Plugin system

## Contributing 🤝

Contributions are welcome! Areas where help is needed:

- Feature implementation
- Documentation
- Testing
- Performance optimization
- Example projects

### Development Commands

```bash
# Quick development commands
make help           # Show all available commands
make build          # Build the project
make test           # Run tests
make docs           # Build documentation
make serve-docs     # Serve docs locally

# Release commands (maintainers only)
make release-dry    # Preview what a release would do
make release-patch  # Create a patch release
make release-minor  # Create a minor release
make release-major  # Create a major release
```

## License 📄

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support 💬

- Create an issue for bugs or feature requests
- Join our Discord server (coming soon)
- Check the [documentation](https://docs.rs/rustycord) (coming soon)

## Acknowledgments 🙏

- Inspired by [serenity-rs](https://github.com/serenity-rs/serenity)
- Built with [tokio](https://tokio.rs/) and [serde](https://serde.rs/)
- Thanks to the Discord API team for excellent documentation

---

Made with ❤️ and 🦀 by the rustycord team
