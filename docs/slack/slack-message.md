# Slack Message

This document provides examples and guidelines for building Slack messages using Block Kit Builder and sending messages
via Incoming Webhooks and Slack API.

---

## Slack Block Kit Builder

You can use the Slack Block Kit Builder app to visually design and test your Slack message layouts:  
[https://app.slack.com/block-kit-builder](https://app.slack.com/block-kit-builder)

Try changing properties like `color` in attachments or customizing blocks for your needs.

---

## Incoming Webhooks

Slack Incoming Webhooks allow you to send messages to Slack channels via HTTP requests.  
You need to create an app and enable Incoming Webhooks in your Slack workspace:  
[https://api.slack.com/apps](https://api.slack.com/apps)

Example Incoming Webhook URL:  
`https://hooks.slack.com/services/XXXXXXXXX/XXXXXXXXX/XXXXXXXXXXXXXXXXXXXX`

---

## Example Slack Messages Using Block Kit JSON

### Reminder Message (Start of Event)

```json
{
  "blocks": [
    {
      "type": "header",
      "text": {
        "type": "plain_text",
        "text": "🌟 Friendly Reminder: Upcoming Team Meeting :rocket:",
        "emoji": true
      }
    },
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "Hi Team,\n\nJust a quick reminder about our upcoming *Team Meeting*:\n\n*📌 Topic:* Efficient Build System Updates\n*🗓️ Date:* 22.11.2024\n*⏰ Time:* 15:00-16:00\n*📍 Link:* <https://meeting-link.example.com|Click here to join the meeting>\n\nLooking forward to seeing you all there! 😊"
      }
    }
  ]
}
```

### Thank You Message (End of Event)

```json
{
  "blocks": [
    {
      "type": "header",
      "text": {
        "type": "plain_text",
        "text": "👏 Great Job, Team! 🎉",
        "emoji": true
      }
    },
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "Thank you everyone for your participation in today’s Team Meeting :rocket:! Special thanks to our presenters for their excellent work:\n\n*🗣️ Presenter 1* - for the *Build System Overview* presentation 👏\n*🗣️ Presenter 2* - for the *Automation Tools* presentation 👏\n\nPlease share your topic ideas in the #team-announcements channel. Looking forward to the next meeting! 🚀"
      }
    },
    {
      "type": "divider"
    },
    {
      "type": "context",
      "elements": [
        {
          "type": "plain_text",
          "text": "Thanks again, everyone! Keep up the fantastic work! 💪🏼",
          "emoji": true
        }
      ]
    }
  ]
}
```

### Example with User Mentions

In Slack Block Kit, you can mention users by their Slack User ID:

```json
{
  "type": "section",
  "text": {
    "type": "mrkdwn",
    "text": "Special shoutout to our presenter <@U12345678> for an amazing presentation! 🎉"
  }
}
```

Replace `U12345678` with the actual Slack User ID.

### Example: User Joining Request Message

```json
{
  "blocks": [
    {
      "type": "header",
      "text": {
        "type": "plain_text",
        "text": "User Request to Join Channel",
        "emoji": true
      }
    },
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "Hello @here, <@U12345678> would like to join this private channel but cannot. Could someone please add them?"
      }
    }
  ]
}
```

---

## Sending Messages Using `curl` and Incoming Webhooks

Replace `YOUR_WEBHOOK_URL` with your actual webhook URL.

### Send Reminder Message

```bash
curl -X POST -H 'Content-type: application/json' --data '{
  "blocks": [
    {
      "type": "header",
      "text": {
        "type": "plain_text",
        "text": "🌟 Friendly Reminder: Upcoming Team Meeting :rocket:",
        "emoji": true
      }
    },
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "Hi Team,\n\nJust a quick reminder about our upcoming *Team Meeting*:\n\n*📌 Topic:* Efficient Build System Updates\n*🗓️ Date:* 22.11.2024\n*⏰ Time:* 15:00-16:00\n*📍 Link:* <https://meeting-link.example.com|Join here>\n\nLooking forward to seeing you all there! 😊"
      }
    }
  ]
}' https://hooks.slack.com/services/YOUR_WEBHOOK_URL
```

### Send Thank You Message

```bash
curl -X POST -H 'Content-type: application/json' --data '{
  "blocks": [
    {
      "type": "header",
      "text": {
        "type": "plain_text",
        "text": "👏 Great Job, Team! 🎉",
        "emoji": true
      }
    },
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "Thank you everyone for your participation in today’s Team Meeting :rocket:! Special thanks to our presenters for their excellent work:\n\n*🗣️ Presenter 1* - for the *Build System Overview* presentation 👏\n*🗣️ Presenter 2* - for the *Automation Tools* presentation 👏\n\nPlease share your topic ideas in the #team-announcements channel. Looking forward to the next meeting! 🚀"
      }
    },
    {
      "type": "divider"
    },
    {
      "type": "context",
      "elements": [
        {
          "type": "plain_text",
          "text": "Thanks again, everyone! Keep up the fantastic work! 💪🏼",
          "emoji": true
        }
      ]
    }
  ]
}' https://hooks.slack.com/services/YOUR_WEBHOOK_URL
```

---

## Emoji Customization

You can use custom emojis in your messages.
Manage your workspace emojis here:
`https://YOURWORKSPACE.slack.com/customize/emoji`

Use emojis by their shortcodes, for example: `:rocket:`, `:star:`, `:heart:`.

---

## Helpful Links

- Slack Block Kit Builder: [Slack Block Kit Builder](https://app.slack.com/block-kit-builder)
- Slack API Incoming Webhooks: [Slack Webhooks](https://api.slack.com/messaging/webhooks)
- Custom Emojis: `https://YOURWORKSPACE.slack.com/customize/emoji`
