# Slack Notifications via GitHub Actions

This document contains example GitHub Actions workflows that send notifications to Slack channels using
`slackapi/slack-github-action@v1.27.0`.

---

## 1. Notify Slack on New Discussion

This workflow triggers when a new discussion is created in the repository and sends a formatted message to a Slack
channel.

<details>
<summary>discussion-notify.yml</summary>

```yaml
name: Notify Slack on New Discussion

on:
  discussion:
    types: [ created ]

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Send Notification To Slack Channel
        uses: slackapi/slack-github-action@v1.27.0
        with:
          payload: '{
            "attachments": [
              {
                "color": "#36C5F0",
                "blocks": [
                  {
                    "type": "section",
                    "text": {
                      "type": "mrkdwn",
                      "text": ":speech_balloon: *New Discussion Created*:\n*<${{ github.event.discussion.html_url }}|${{ github.event.discussion.title }}>*"
                    }
                  }
                ]
              }
            ]
          }'
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_DEVELOPERS_WEBHOOK }}
          SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
```

</details>

---

## 2. Notify Slack to Remind Session

This workflow is triggered manually and sends a reminder about an upcoming Engineering Session.

<details>
<summary>session-reminder-notify.yml</summary>

```yaml
name: Notify Slack to Remind Session

on:
  workflow_dispatch:
    inputs:
      topic:
        description: 'Topic'
        required: true
        type: string
      meet-link:
        description: 'Meet Link'
        required: true
        type: string
      date:
        description: 'Date (e.g. 19.04.2025)'
        required: false
      time:
        description: 'Time (e.g. 14:00 - 15:00)'
        required: false
        default: '14:00 - 15:00'
      session-details-link:
        description: 'Session details Slack link'
        required: false
        type: string
      test:
        description: 'Is test notification?'
        required: true
        type: boolean
        default: true

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Set webhook based on test input
        id: set-webhook
        run: |
          if [ "${{ inputs.test }}" = "true" ]; then
            echo "url=${{ secrets.SLACK_PERSONAL_TEST_WEBHOOK }}" >> $GITHUB_OUTPUT
          else
            echo "url=${{ secrets.SLACK_DEVELOPERS_WEBHOOK }}" >> $GITHUB_OUTPUT
          fi
      - name: Set date
        id: set-date
        run: |
          if [ -z "${{ inputs.date }}" ]; then
            TODAY=$(TZ=Europe/Istanbul date +'%d.%m.%Y')
            echo "date=$TODAY" >> $GITHUB_OUTPUT
          else
            echo "date=${{ inputs.date }}" >> $GITHUB_OUTPUT
          fi
      - name: Prepare session details text
        id: set-details
        run: |
          if [ -n "${{ inputs.session-details-link }}" ]; then
            echo "details_text=:information_source: *Session Details:* <${{ inputs.session-details-link }}|Click here to view session details>" >> $GITHUB_OUTPUT
          else
            echo "details_text=" >> $GITHUB_OUTPUT
          fi
      - name: Send Notification To Slack Channel
        uses: slackapi/slack-github-action@v1.27.0
        with:
          payload: '{
            "unfurl_links": false,
            "unfurl_media": false,
            "blocks": [
                {
                    "type": "header",
                    "text": {
                        "type": "plain_text",
                        "text": "🌟 Friendly Reminder: Upcoming Engineering Session :coffee:",
                        "emoji": true
                    }
                },
                {
                    "type": "section",
                    "text": {
                        "type": "mrkdwn",
                        "text": "Hi Friends,\n\nJust a quick reminder about our upcoming *Engineering Session*:\n\n*📌 Topic:* ${{ inputs.topic }}\n*🗓️ Date:* ${{ steps.set-date.outputs.date }}\n*⏰ Time:* ${{ inputs.time }}\n*📍 Link:* <${{ inputs.meet-link }}|Click here to join the meeting>\n\n${{ steps.set-details.outputs.details_text }}\n\nLooking forward to seeing you all there! 😊"
                    }
                }
            ]
        }'
        env:
          SLACK_WEBHOOK_URL: ${{ steps.set-webhook.outputs.url }}
          SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
```

</details>

---

## 3. Notify Slack for Kudos Session

This workflow is triggered manually and sends a message to Slack to celebrate and thank presenters for an Engineering
Session.

<details>
<summary>session-kudos-notify.yml</summary>

```yaml
name: Notify Slack to Kudos Session

on:
  workflow_dispatch:
    inputs:
      topic:
        description: 'Topic'
        required: true
        type: string
      slack-member-ids:
        description: 'Slack Member IDs (comma-separated, e.g. U123ABCD,U456DEFG)'
        required: true
        type: string
      session-number:
        description: 'Session Number (e.g. 15)'
        required: true
        type: string
      test:
        description: 'Is test notification?'
        required: true
        type: boolean
        default: true

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Set webhook based on test input
        id: set-webhook
        run: |
          if [ "${{ inputs.test }}" = "true" ]; then
            echo "url=${{ secrets.SLACK_PERSONAL_TEST_WEBHOOK }}" >> $GITHUB_OUTPUT
          else
            echo "url=${{ secrets.SLACK_DEVELOPERS_WEBHOOK }}" >> $GITHUB_OUTPUT
          fi
      - name: Parse speaker list
        id: parse-speakers
        run: |
          speakers="${{ inputs.slack-member-ids }}"
          IFS=',' read -ra ADDR <<< "$speakers"
          OUTPUT=""

          for id in "${ADDR[@]}"; do
            OUTPUT="$OUTPUT\\n*🗣️ <@$id>* :crown:\n"
          done

          echo "formatted_speakers=$OUTPUT" >> $GITHUB_OUTPUT
      - name: Send Notification To Slack Channel
        uses: slackapi/slack-github-action@v1.27.0
        with:
          payload: '{
          "unfurl_links": false,
          "unfurl_media": false,
          "blocks": [
            {
              "type": "header",
              "text": {
                "type": "plain_text",
                "text": "👏 Great Job, Friends! 🎉",
                "emoji": true
              }
            },
            {
              "type": "section",
              "text": {
                "type": "mrkdwn",
                "text": "A huge thank you to everyone for your time and contributions in today’s Engineering Session :coffee:! Special shoutout to the amazing presenter(s) who did an outstanding job:\n${{ steps.parse-speakers.outputs.formatted_speakers }}\n\n for the *${{ inputs.topic }}* presentation 👏"
              }
            },
            {
              "type": "section",
              "text": {
                "type": "mrkdwn",
                "text": " #️⃣ Please join the #engineering-sessions channel and give a suggestion for the next topics. Looking forward to the next one! 🚀"
              }
            },
            {
              "type": "section",
              "text": {
                "type": "mrkdwn",
                "text": "*📂 You can check out all previous session materials right <https://yourorg.atlassian.net/wiki/spaces/EN/pages/123456/Previous+Sessions|here.>*"
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
                  "text": "Thanks again, everyone! This was our ${{ inputs.session-number }}. :coffee: session—keep up the fantastic work! 💪",
                  "emoji": true
                }
              ]
            }
          ]
        }'
        env:
          SLACK_WEBHOOK_URL: ${{ steps.set-webhook.outputs.url }}
          SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
```

</details>

---

## Notes

- All workflows require a valid **Slack Incoming Webhook** URL stored in GitHub Secrets.
- Use test webhooks for development to avoid spamming real channels.
- Customize the payload structure for your team's formatting preferences.
- The action version used here is `slackapi/slack-github-action@v1.27.0`.