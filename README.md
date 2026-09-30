# Reps: Gym Workout Log (repsworkout.com) — MCP server

> **This is a hosted service.** This repository holds documentation only. The server's source code is not public, and there is nothing to install or run yourself.

Reps: Gym Workout Log (repsworkout.com) is a gym workout log for iPhone and Android. Core logging in the app is free and works offline. With a free Reps account, the Reps MCP server lets an AI assistant such as Claude or ChatGPT read your training log and answer from your own numbers. When you confirm, it can also save routines and plans back to your account.

[![Reps MCP connector](https://glama.ai/mcp/connectors/com.repsworkout/reps/badges/score.svg)](https://glama.ai/mcp/connectors/com.repsworkout/reps)

## Connect

**Server URL:** `https://api.repsworkout.com/mcp` (Streamable HTTP, OAuth sign-in)

Setup guide with screenshots and troubleshooting: [repsworkout.com/connect](https://repsworkout.com/connect?utm_source=github)

- **ChatGPT.** Install Reps from the [ChatGPT plugin directory](https://chatgpt.com/plugins/plugin_asdk_app_6aa468aa30348191accb23ecd716488a), then select Connect. In a chat, type `@Reps` and ask about your training.
- **Claude (web, desktop and mobile).** Open Customize → Connectors → Add custom connector. Name it Reps and paste the server URL. Select Connect and sign in. On Team and Enterprise plans, an organisation owner adds the connector.
- **Claude Code.** Run this, then run `/mcp`, pick `reps` and sign in in your browser:

  ```sh
  claude mcp add --transport http reps https://api.repsworkout.com/mcp
  ```

- **Other MCP clients.** The sign-in accepts redirects back to Claude, ChatGPT and Glama, and to clients on your own machine that use a loopback address (`localhost` or `127.0.0.1`), such as MCP Inspector. If your client uses another hosted callback, write to support@repsworkout.com.

## Account

You need a Reps account. If you don't have one, the sign-in page offers to create a free one while you connect, with an e-mail code, Apple or Google. It is the same account the app uses, and you must be 16 or older. Afterwards the page shows where to get the app.

You log your workouts in the app:

- [App Store (iPhone)](https://apps.apple.com/app/apple-store/id6761923928?pt=128748625&ct=github&mt=8)
- [Google Play (Android)](https://play.google.com/store/apps/details?id=com.repsworkout.reps&referrer=utm_source%3Dgithub)

The app sends your data to your account every few minutes and whenever you open it, so the assistant sees a workout shortly after you finish it.

## Tools

14 tools: 8 read your data and 6 write to your account. Every tool has a `title` and MCP annotations. The read tools are marked `readOnlyHint: true`. Tools that replace or stop something you already had are marked `destructiveHint: true`.

| Tool | Title | Access | What it does |
|---|---|---|---|
| `get_workout_history` | Workout history | read | Your finished workouts with exercises and sets, newest first (default: last 30 days) |
| `get_exercise_progression` | Exercise progression | read | One exercise over time: max weight, estimated 1RM or volume (default: last 90 days) |
| `get_personal_records` | Personal records | read | The records the app has tracked, for one exercise or all of them |
| `get_training_plan` | Active training plan | read | Your active plan with its routines, and which routine is next |
| `get_routines` | Saved routines | read | Your saved routines with their exercises and the plans that use them |
| `get_weekly_summary` | Weekly summary | read | One week's workouts, volume, duration and muscle-group split |
| `get_exercise_library` | Exercise library | read | Built-in and custom exercises, searchable in any of the app's languages, with your note on each |
| `get_stats_overview` | Stats overview | read | Totals for a date range: workouts, volume, weekly trend, muscle groups and most-trained exercises |
| `save_routine` | Save a routine | write | Saves a new routine, after you confirm it in the chat |
| `save_plan` | Save a training plan | write, destructive | Saves a whole plan, after you confirm, and by default makes it the active plan. The plan it replaces stops being active |
| `edit_routine` | Edit a routine | write, destructive | Changes a routine's exercises, sets or targets, after you confirm. Refuses if the routine changed in the app since it was read |
| `add_custom_exercise` | Add a custom exercise | write | Adds an exercise the library doesn't have to your own library |
| `update_custom_exercise` | Edit a custom exercise | write, destructive | Renames or re-files one of your own custom exercises, or edits its how-to |
| `set_exercise_note` | Set an exercise note | write, destructive | Writes, replaces or clears your own note on an exercise |

The server's instructions and each save tool tell the assistant to describe a routine, plan or routine change and wait for your yes before saving it. Saved changes appear in the app after its next sync, or straight away when you open it.

**What it can't do.** It can't log a workout for you: you log workouts in the app. It can't delete your workout history or your account. Reps holds only what you log in the app: exercises, sets, reps, weights, durations, dates, notes, routines and plans. There is no heart rate, pace, GPS, sleep or nutrition data, and nothing is imported from watches or other apps.

## Try asking

- "How did my training go last week?"
- "How has my bench press progressed over the last three months?"
- "What are my current personal records?"
- "What should I train today?"
- "Build me a 3-day beginner gym plan and save it to Reps."
- "In my Push routine, swap the dumbbell fly for a cable fly."

## Authentication

- OAuth 2.1 authorization code flow with PKCE (`S256`) and dynamic client registration.
- Protected resource metadata: `https://api.repsworkout.com/.well-known/oauth-protected-resource/mcp`
- Authorization server metadata: `https://api.repsworkout.com/.well-known/oauth-authorization-server`
- Scopes: `read` and `write`. Access tokens expire after 30 days and are renewed while you keep using the connection. A connection left unused lapses after 180 days.
- There is no password. You sign in on the Reps page at `api.repsworkout.com` with a one-time e-mail code, Apple or Google, and the assistant never sees the code.

## Privacy

- The assistant reads your training data only while you chat with Reps turned on, and only what it asks for.
- What it reads goes to the company behind that assistant (for example Anthropic or OpenAI) under your agreement with them. Reps doesn't control what they do with it.
- Your Reps data is stored in the EU (Supabase database, server on Railway, both in EU regions). For each connection, Reps stores hashes of the tokens, your encrypted sign-in session, which assistant it is and when it was last used.
- Server logs hold request metadata and a hashed user ID, never your training data or chat content.
- To disconnect, remove Reps in the assistant's settings. To revoke every connection at once, e-mail privacy@repsworkout.com or delete your Reps account, which ends them immediately.

Full details: [Privacy Policy](https://repsworkout.com/privacy), section "Connecting Claude, ChatGPT or another AI assistant". Terms: [repsworkout.com/terms](https://repsworkout.com/terms).

## Listings

- ChatGPT: [Reps in the plugin directory](https://chatgpt.com/plugins/plugin_asdk_app_6aa468aa30348191accb23ecd716488a)
- Official MCP Registry: `com.repsworkout/reps`
- Glama: [glama.ai/mcp/connectors/com.repsworkout/reps](https://glama.ai/mcp/connectors/com.repsworkout/reps)

## Support

- Support: support@repsworkout.com
- Privacy requests: privacy@repsworkout.com
- Website: [repsworkout.com](https://repsworkout.com/?utm_source=github)

Reps is made by Szymon Łukiewicz, an independent developer in Poland.
