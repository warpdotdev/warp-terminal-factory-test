---
name: slack
description: "Mechanics for communicating on Slack: posting and replying in threads through the connected Slack MCP server, direct messages to a specific person, user lookup, mrkdwn formatting, links and unfurls, user mentions, and the reactions that mark progress on the original request message. Use when the conversation with the user happens on Slack, for example when the request arrived from a Slack thread, or when the task asks you to notify someone on Slack."
---

# slack

How to use Slack. This skill covers the mechanics of the surface, not when to communicate or what to say.

## Delivery is not automatic

Nothing you write reaches Slack on its own. Your final response, a run summary, and `finish_task`/`complete_task` close out the run — they are not seen by Slack. The only way anything reaches the thread is calling `send_message_to_thread` or `send_message_to_channel` yourself, before you finish. Drafting a reply in your own output and never sending it looks identical, from Slack's side, to never having drafted one at all.

## Tools

All Slack operations go through the connected Slack MCP server. Do not use a Slack CLI or raw API calls when an MCP tool exists for the operation. The usual tool names:
- Reply inside an existing thread: `send_message_to_thread`. Post a new top-level message: `send_message_to_channel`.
- Message a person directly: `send_direct_message`, with their Slack user ID. See the direct messages section below.
- Post a file inline (for example a verification video): `upload_file`, with the artifact UID of a run artifact. See the computer-use media section below.
- Read a thread: `read_thread`. Read recent channel messages: `read_channel`.
- Reactions: `add_reaction`, `remove_reaction`, `read_reactions`.
- Thread status: `assistant_threads_set_status`.
- User lookup: `find_user_by_email` by exact email, `search_users` by name, and `read_user` to turn a user ID into a profile.
The connected server can name its tools differently. When a name does not match, list the server's tools and pick the tool for the operation.

## Where to post

- Reply in the thread that the request came from. Do not start a new top-level message for work that already has a thread.
- Post a new top-level channel message only when the task is explicitly to produce one, for example a scheduled report.
- Read anywhere, write in one place. Read any channel or thread you have access to for context. Post only in the thread that asked.
- Another thread is not a destination, even when the request links to it, even when it discusses the same work, and even when you were just added to its channel. Being told to read a thread is not a license to write in it.
- Bring what you learn elsewhere back to the thread that asked. Post in another thread only when the user explicitly asks you to post there.

## Direct messages

- Message a person directly only when the request or your instructions ask you to notify or message that specific person. Being mentioned, assigned, or involved in the work is not a reason to DM someone.
- Resolve the recipient first: `find_user_by_email` when you have their email, otherwise `search_users` by name. When a name search returns more than one plausible person, do not guess; ask in the thread that asked instead.
- `send_direct_message` posts into the factory app's DM with that person and returns the DM's `channel_id` and `message_ts`. Send any follow-up with `send_message_to_thread` using those values, so the exchange stays in one thread.
- A DM does not replace the reply in the thread that asked. When you message someone on the requester's behalf, say so in that thread.

## Computer-use media

- A computer-use capture reaches Slack as a file uploaded with `upload_file`, not as a link. A bare link to a capture is the fallback, for when the upload is genuinely not possible.
- `upload_file` takes `artifact_uid`, `channel_id`, and `thread_ts` for a thread reply, plus an optional `title` and `initial_comment`.
- The server refuses an artifact that another run produced, so the artifact you name must be one of your own run's.
- The `ui-verification` skill owns the rest: what to capture, which capture belongs in a thread, and how to take ownership of a capture that another agent's run produced.

## Formatting (mrkdwn, not markdown)

Slack renders mrkdwn. Standard markdown renders as literal characters.
- Bold: `*text*` with single asterisks. `**text**` does not render.
- Italic: `_text_`.
- Links: `<https://example.com|label>`. Markdown `[label](url)` does not render.
- Bullets: the `•` character. `-` and `*` do not render as list markers.
- Code: backticks and triple-backtick blocks render as in markdown.
- Headers: there are none. `#` does not render. Use a short bold line in place of a header.
- Mentions: `<@USER_ID>` with the Slack user ID, not the display name. Resolve the ID with `find_user_by_email` when you have their email, otherwise `search_users`.

## Slack identities outside Slack

Before writing a Slack user ID into a non-Slack artifact (a Linear or Jira issue, a GitHub artifact, a report), resolve it with `read_user` and write `Display Name (Slack ID)` — never the bare ID, and never an email inferred from the ID.

## Links and unfurls

- A bare URL unfurls into a large preview card. Use `<url|label>` to prevent it.
- Link every distinct PR, issue, and spec you name, in every message that names it, however many that is: a message naming twelve pull requests carries twelve links. Nothing caps how many links one message holds. Slack does not turn `#NNN` into a link the way a code forge does; a bare number stays plain text, so write it as `<url|#NNN>`.
- Label a reference outside the thread's primary repo as `owner/repo#NNN`. The number alone is ambiguous.
- The only limit is on duplicates, and it is not a limit on how many links a message carries: when one message names the same target more than once, link its first mention and leave the later ones bare. Distinctness is by target, so two different PR numbers are two different targets and each gets its own link - one link never fills a message's quota, because there is no quota. The scope is the message, not the thread: a later message that names the target links it again, so no reader scrolls back up for the clickable copy. `<url|label>` carries no preview card, so re-linking costs nothing.
- Always suppress unfurls on Warp links (agent run links, warp.dev links). Write them as `<url|label>`.

## Thread status

- When the MCP server provides the assistant thread status tool (`assistant_threads_set_status`), set a short status while work is in progress (for example "working on it"). The integration may have already set one (for example "is thinking…") before you start; overwrite it with something more specific once you know what you're doing.
- Clear the status (set it to empty) when your activity ends, on success and on failure — even when you never set it yourself, because the integration's status is still yours to clear. A status left in place, whoever set it, looks like a hung agent.

## Reactions on the original request

The factory marks the original request message with reactions. The original request is the message that started the thread. Use its timestamp as `message_ts`. A follow-up message uses its own timestamp. At most one factory reaction is on a message at any time. Every new reaction replaces the previous factory reaction on that message, including the integration `:eyes:` reaction and the terminal reaction. The reaction reads as the current state, not as a progress bar. Do every reaction operation with the MCP tools `add_reaction`, `remove_reaction`, and `read_reactions`.

- Set the stage reaction when the factory enters a stage, replacing whatever factory reaction is on the message. The stages are `:mag:` for triage, `:memo:` for spec, `:hammer:` for implementation, and `:arrows_counterclockwise:` for code review.
- Set it the same way when the factory enters a stage it was in before, for example implementation again after code review. The reaction reflects the stage the factory is in.
- Set no reaction for a stage that the factory skips.
- When the factory task ends, replace the current factory reaction with one terminal reaction: `:white_check_mark:` for success, or `:x:` for cancelled or abandoned work. The terminal reaction is what stays on the message.
- Add the new reaction first, then remove the previous one, so the message is never momentarily unmarked.
- Remove only the emoji names in this section: the stage reactions, the terminal reactions, and the integration `:eyes:` reaction. Never remove a reaction from a person. `remove_reaction` acts as the connected app, so it removes only that app's own reaction.
- Read the reactions with `read_reactions` before each remove. Remove only when that emoji name is present with a count above zero.

### Integration acknowledgement cleanup

The Slack integration adds an `:eyes:` reaction to a message when it delivers that message to the factory. It marks the message as received and it is the first element of the single-reaction sequence.

- The first stage reaction the factory sets replaces it. When no stage runs, for example a question answered directly, the terminal reaction replaces it. Either way the `:eyes:` reaction is gone once the reply to that message posts.
- Apply this to the original request. Apply it also to each follow-up message that triggered a delivery. A follow-up carries its own `:eyes:` on its own timestamp, and the one-reaction rule holds per message.
- Keep the `:eyes:` reaction when the reply fails to post.

### Best effort

Reaction operations never fail the run. Treat `already_reacted`, `no_reaction`, and MCP errors as silent non-errors. Every step is idempotent, so it is safe to repeat.

## Message shape

- Aim for 8 lines and 500 characters. Never exceed 12 lines or 800 characters. Both counts are of the rendered text a reader sees, not of the mrkdwn payload: `<https://github.com/o/r/pull/98|#98>` counts as the three characters of `#98`, so link markup never consumes the budget. Put the action or result in the first line. Put detail behind links to the PR, the issue, or the spec.
- A link is never the thing you cut to fit. When a message runs long, cut words, cut detail, or name fewer targets and point at where the full list lives. Every target that stays in the message keeps its link.
- One message per event. Do not split one update into several messages.
