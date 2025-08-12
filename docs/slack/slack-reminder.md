# Slack Reminder

Slack reminders allow you to schedule notifications for yourself, specific channels, or groups at specific times or on a
recurring basis.  
They are useful for meeting reminders, daily check-ins, and recurring tasks.

---

## Syntax

```
/remind [@someone or #channel] "[message]" [time or recurrence]
```

- `@someone` → Sends the reminder to a specific person.
- `#channel` → Sends the reminder to an entire channel.
- `[message]` → The text to display when the reminder triggers.
- `[time or recurrence]` → When the reminder should occur (e.g., "at 9am", "every Monday", "tomorrow", "in 10 minutes").

---

## Examples

### 1. Remind Yourself Once

```
/remind me "Check project status updates" at 3pm today
```

### 2. Remind a Channel Once

```
/remind #general "Team meeting starts in 10 minutes" at 2:50pm
```

### 3. Daily Recurring Reminder

```
/remind me "Read daily stand-up notes" every weekday at 9am
```

### 4. Weekly Recurring Reminder

```
/remind #project-team "Weekly sync meeting starts now" every Monday at 10am
```

### 5. Every Few Weeks

```
/remind #design-team "Submit design review notes" every 2 weeks on Monday at 4pm
```

### 6. Other Examples

```text
-- Weekly team meeting (every 2 weeks)
/remind #team "@here Please add your items to the meeting agenda." at 9am on Monday, December 2, 2024 every 2 weeks

-- Another meeting time variation
/remind #team "@here Don't forget to review the agenda before the meeting." at 3pm on Monday, December 2, 2024 every 2 weeks

-- Morning motivational reminder
/remind @username every weekday at 7:30am "Read your daily motivation rules before starting the day!"

-- Early daily start
/remind @username every weekday at 6:00am "Good morning! Let's start the day with a positive mindset."

-- Pre-event technical check
/remind @username every Friday at 10:15am "Check audio and video permissions before the engineering session."

-- Event reminder
/remind @username every Friday at 2:45pm "Friendly reminder: send Slack notification for the engineering session."
```

---

## Tips

- Quotes (`" "`) around the reminder message are recommended, especially if it contains spaces.
- You can use `me` instead of your username to send reminders to yourself.
- Use channel names like `#general` or `#team-meeting` to notify everyone in that channel.
- Common recurrence formats:
    - `every day at [time]`
    - `every weekday at [time]`
    - `every [number] weeks on [day] at [time]`
    - `tomorrow at [time]`
    - `in [number] minutes/hours`

---

## Managing Reminders

- To see your upcoming reminders:

```
/remind list
```

- To delete a reminder:
    - Use `/remind list` to find it and then select **Delete**.

---

## Quick Reference Table

| Command Example                                                          | Description                                       |
|--------------------------------------------------------------------------|---------------------------------------------------|
| `/remind me "Submit timesheet" every Friday at 5pm`                      | Reminds you every Friday to submit your timesheet |
| `/remind #marketing "Campaign kickoff call" at 11am tomorrow`            | Notifies the marketing channel about the kickoff  |
| `/remind me "Stretch break" every hour`                                  | Sends you a stretch reminder every hour           |
| `/remind #support-team "Review support tickets" every weekday at 9:30am` | Daily ticket review reminder for the support team |

