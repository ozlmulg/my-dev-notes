# Slack User Group Creation

## Creating Slack User Groups via API

You can create user groups in Slack to mention multiple users at once efficiently.

### Example `curl` request to create a user group

```bash
curl -X POST https://slack.com/api/usergroups.create \
-H "Authorization: Bearer xoxb-your-bot-token" \
-H "Content-Type: application/json; charset=utf-8" \
-d '{
  "name": "Test User Group",
  "handle": "test-user-group",
  "description": "Test user group"
}'
```

Replace `xoxb-your-bot-token` with your bot user OAuth token with `usergroups:write` scope.

---

## Automation with Makefile target and Bash script

You can automate creating user groups and connecting them to channels using a Makefile target and a shell script.

**Makefile target example:**

```makefile
create-slack-user-groups: ## Create slack user groups and connect to channel
	@bash .make/scripts/create-slack-user-groups.sh
```

**Bash script example:**

<details>
<summary>.make/scripts/create-slack-user-groups.sh</summary>

```bash
#!/bin/bash

# Slack OAuth Token
SLACK_TOKEN="xoxb-XXXX-XXXX-XXX"

# User Group and Channel list
declare -A TEAMS
TEAMS=(
  ["team_name_1"]="channel_name_1"
  ["team_name_2"]="channel_name_2"
)

# API URLs
CREATE_USERGROUP_URL="https://slack.com/api/usergroups.create"
UPDATE_USERGROUP_URL="https://slack.com/api/usergroups.update"
GET_CHANNELS_URL="https://slack.com/api/conversations.list"

# Function to get channel ID by name
get_channel_id() {
  local channel_name=$1
  local response=$(curl -s -X GET "$GET_CHANNELS_URL" \
    -H "Authorization: Bearer $SLACK_TOKEN" \
    -H "Content-Type: application/json; charset=utf-8")

  echo "$response" | jq -r --arg name "$channel_name" '.channels[] | select(.name == $name) | .id'
}

# Create User Groups and connect to channels
for team_name in "${!TEAMS[@]}"; do
  channel_name=${TEAMS[$team_name]}

  # Get channel id
  channel_id=$(get_channel_id "$channel_name")
  if [[ -z "$channel_id" ]]; then
    echo "Error: Channel not found -> $channel_name"
    continue
  fi

  # Create User Group
  create_response=$(curl -s -X POST "$CREATE_USERGROUP_URL" \
    -H "Authorization: Bearer $SLACK_TOKEN" \
    -H "Content-Type: application/json; charset=utf-8" \
    -d '{
      "name": "'"$team_name"'",
      "handle": "'"$team_name"'",
      "description": "Description for '"$channel_name"'"
    }')

  usergroup_id=$(echo "$create_response" | jq -r '.usergroup.id')
  if [[ "$usergroup_id" == "null" ]]; then
    echo "Error: User group can not be created -> $team_name"
    echo "Error detail: $(echo "$create_response" | jq -r '.error')"
    continue
  fi

  # Connect User Group to Channel
  update_response=$(curl -s -X POST "$UPDATE_USERGROUP_URL" \
    -H "Authorization: Bearer $SLACK_TOKEN" \
    -H "Content-Type: application/json; charset=utf-8" \
    -d '{
      "usergroup": "'"$usergroup_id"'",
      "channels": "'"$channel_id"'"
    }')

  if [[ $(echo "$update_response" | jq -r '.ok') == "true" ]]; then
    echo "Success: $team_name is connected to $channel_name channel."
  else
    echo "Error: Connecting user group failed -> $team_name"
    echo "Error detail: $(echo "$update_response" | jq -r '.error')"
  fi
done
```

</details>

---

## Notes

- Make sure your bot token has the required scopes (`usergroups:write` and `channels:read` or `conversations:read`).
- This script requires `jq` installed for JSON parsing.
- Customize the `TEAMS` associative array with your user group names and corresponding Slack channel names.

