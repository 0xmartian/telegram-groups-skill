# Telegram Groups Skill for OpenClaw

Enable your OpenClaw AI agent to participate in Telegram group chats with full message access and active engagement.

## What This Skill Does

This skill documents how to configure OpenClaw to:
- Receive and respond to messages in Telegram group chats
- Set up proper bot permissions and privacy settings
- Control which groups and users can interact with your agent
- Configure response behavior (mention-only vs all messages)

## Quick Start

1. Configure bot privacy via @BotFather (Group Privacy → OFF)
2. Add bot to your group as an admin
3. Get the group chat ID from a message link
4. Update OpenClaw config with group allowlist
5. Restart gateway

See [SKILL.md](SKILL.md) for detailed step-by-step instructions.

## Installation

This skill is documentation-only (no code to install). Simply:

1. Read [SKILL.md](SKILL.md) for the complete setup guide
2. Follow the configuration steps for your OpenClaw instance
3. Add your Telegram groups to the allowlist

## Example Configuration

```json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "groupPolicy": "allowlist",
      "groups": {
        "-1003538637138": {
          "enabled": true,
          "requireMention": false,
          "allowFrom": ["*"]
        }
      }
    }
  }
}
```

## Use Cases

- **Developer teams**: AI assistant in your dev chat
- **Community support**: Automated help in user groups
- **Content moderation**: Active monitoring and response
- **Social presence**: Agent participation in community discussions

## Requirements

- OpenClaw with Telegram plugin enabled
- Telegram bot token from @BotFather
- Admin access to target group chats

## Documentation

See [SKILL.md](SKILL.md) for:
- Complete setup walkthrough
- Troubleshooting common issues
- Security considerations
- Advanced configuration options
- Multiple group examples

## Created By

**Phobos** 🌑 - AI assistant to [Jerry the Martian](https://github.com/jerryfane)

This skill documents the configuration used to enable Phobos in the Moltinex Telegram group.

## Contributing

Found an issue or have an improvement? Open an issue or PR!

## License

MIT
