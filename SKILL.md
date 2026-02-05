# Telegram Group Chat Setup

Enable your OpenClaw agent to participate in Telegram group chats.

## Prerequisites

- Telegram bot token (from @BotFather)
- Admin access to the target group chat
- OpenClaw with Telegram plugin enabled

## Step 1: Configure Bot Privacy Settings

**Via @BotFather:**

1. Message @BotFather on Telegram
2. Send `/mybots`
3. Select your bot
4. Go to **Bot Settings** → **Group Privacy**
5. **Turn OFF** Group Privacy

This allows your bot to see all messages in groups (not just mentions).

## Step 2: Add Bot to Group and Make Admin

1. Add your bot to the target Telegram group
2. Make the bot an **administrator** with at least these permissions:
   - Read messages
   - Send messages
   - Delete messages (optional)

**After changing privacy settings via BotFather:**
- Remove the bot from the group
- Re-add it
- Make it admin again

Privacy changes don't apply retroactively - the bot must rejoin.

## Step 3: Get the Group Chat ID

The group chat ID is needed for the config. Get it from a message link:

```
https://t.me/c/3538637138/123
                ^^^^^^^^^^
            This is the chat ID (without the 'c/')
```

Prefix with `-100` to get the full ID: `-1003538637138`

## Step 4: Update OpenClaw Config

Add the group to your Telegram configuration:

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

**Config fields:**

- `groupPolicy: "allowlist"` - Only listed groups can message the bot
- `groups[chatId].enabled` - Enable this specific group
- `requireMention: false` - Respond to all messages (not just @mentions)
- `allowFrom: ["*"]` - Accept messages from all group members

### Using the Gateway Tool

Update config via the `gateway` tool:

```javascript
gateway.action = "config.patch"
gateway.raw = {
  "channels": {
    "telegram": {
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

## Step 5: Restart Gateway

Full restart required for Telegram plugin to pick up new groups:

```bash
openclaw gateway restart
```

Or via the `gateway` tool (requires `commands.restart: true` in config):

```javascript
gateway.action = "restart"
gateway.reason = "Apply Telegram group configuration"
```

## Step 6: Test

1. Send a message in the group
2. Agent should receive and respond
3. Have other members test to confirm `allowFrom: ["*"]` works

## Troubleshooting

### Bot doesn't see any messages
- Verify bot is an **admin** in the group
- Check Group Privacy is **OFF** in @BotFather
- Remove and re-add bot after changing privacy settings
- Restart OpenClaw gateway

### Bot only sees mentions
- Set `requireMention: false` in group config
- Reload config or restart gateway

### Bot only responds to you
- Add `allowFrom: ["*"]` to group config
- Reload config

### Config changes don't apply
- Use `gateway.action = "restart"` for full restart
- Config reloads (`config.patch`) may not reinitialize Telegram plugin

## Group Chat Behavior

### Message Routing
- Each group gets its own session (isolated history)
- Sessions persist across restarts
- Group context includes recent message history

### Selective Response
Configure when the bot responds:

```json
{
  "requireMention": true,  // Only respond to @mentions
  "allowFrom": ["123456", "789012"]  // Specific user IDs only
}
```

### Per-User Tools
Restrict tools by sender in a group:

```json
{
  "groups": {
    "-1003538637138": {
      "toolsBySender": {
        "123456": {
          "allow": ["exec", "gateway"]
        },
        "*": {
          "deny": ["exec", "gateway"]
        }
      }
    }
  }
}
```

## Multiple Groups

Add multiple groups to the allowlist:

```json
{
  "groups": {
    "-1003538637138": {
      "enabled": true,
      "requireMention": false
    },
    "-1001234567890": {
      "enabled": true,
      "requireMention": true  // Different settings per group
    }
  }
}
```

## Security Considerations

- `allowFrom: ["*"]` trusts all group members
- Consider using `allowFrom: ["userid1", "userid2"]` for sensitive groups
- Use `toolsBySender` to restrict dangerous tools
- Set `requireMention: true` to reduce noise in large groups

## Related Config

- `channels.telegram.groupPolicy` - Overall group access policy
- `channels.telegram.groupAllowFrom` - Global sender allowlist (applies to all groups)
- `channels.telegram.historyLimit` - How many messages to keep in context

## Example: Private Dev Team Group

```json
{
  "channels": {
    "telegram": {
      "groups": {
        "-1003538637138": {
          "enabled": true,
          "requireMention": false,
          "allowFrom": ["123456", "789012", "345678"],
          "tools": {
            "allow": ["exec", "read", "write", "gateway"]
          }
        }
      }
    }
  }
}
```

## Example: Public Community Group

```json
{
  "channels": {
    "telegram": {
      "groups": {
        "-1001234567890": {
          "enabled": true,
          "requireMention": true,
          "allowFrom": ["*"],
          "tools": {
            "deny": ["exec", "gateway", "sessions_spawn"]
          }
        }
      }
    }
  }
}
```

---

**Note:** This skill documents the configuration used to enable Phobos (Martian's agent) in the Moltinex Telegram group.
