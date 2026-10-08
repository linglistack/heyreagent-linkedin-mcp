---
name: heyreagent
description: Work with the user's own LinkedIn account through the HeyReagent MCP server. Use when the user asks who is waiting on a reply, wants a LinkedIn conversation read or answered, wants connection invitations sent, answered or taken back, wants people, companies or posts searched, or wants a post published or commented on, and the heyreagent tools are available.
---

# HeyReagent: LinkedIn on the user's own account

The tools come from the MCP server `heyreagent` at `https://api.heyreagent.com/mcp`. Each one does one thing on the LinkedIn account the user connected at heyreagent.com. HeyReagent is an independent service; it is not made by or affiliated with LinkedIn.

## Before the first call

- If the tools are missing or a call says to sign in, ask the user to sign in to the `heyreagent` server from their app's MCP or plugin settings. Never ask for a password or a key in the chat.
- `get_my_profile` checks the connection and shows which LinkedIn accounts are connected.
- `linkedin_disconnected` or `no_live_account`: run `connect_linkedin` and give the user the link it returns.
- `reconnect_needed`: run `update_linkedin_connection` with the account's `connectionId` from `list_linkedin_accounts`, and give the user the link it returns.

## Ask before anything other people will see

Reading needs no permission. Before any call that sends or changes something on LinkedIn, show the user exactly what will go out and to whom, and wait for a yes. That covers `send_message`, `send_chat_message`, `start_conversation`, `send_invitation`, `respond_to_invitation`, `withdraw_invitation`, `withdraw_invitations`, `create_post`, `comment_on_post`, `react_to_post`, `edit_message`, `delete_message`, `delete_chat`, `endorse_skill`, `update_my_profile`, `save_lead`, and the job-posting and Recruiter actions that create, edit, publish, close, move or reject.

A yes covers what was shown, nothing more. Taking back an invitation and deleting cannot be undone: say so when asking.

## Common tasks

Take every id and link from the answer of an earlier call. Do not guess one.

- **Who needs a reply.** `who_is_waiting_on_me`. If the answer names accounts under `needsInboxSync`, run `sync_inbox` first. To answer a row, pass its `profileUrl` to `send_message`.
- **Read a conversation and reply.** `list_chats` with `unread: true`, then `list_chat_messages` with the `chatId`, then `send_chat_message` into the same `chatId`. Or `get_conversation` with the person's profile link.
- **Message someone.** `send_message` with `profileUrl` or `conversationId`, one of them. An ordinary message reaches only a 1st-degree connection; for anyone else, `send_invitation` first.
- **Find people and invite them.** `search_people`, then one `send_invitation` per person with `people[].profileUrl`. Keep the `invitationId` in case the user wants it taken back.
- **Search with a LinkedIn filter.** Filters take LinkedIn's own ids. `lookup_search_ids` gives the id (for example type `LOCATION`, keywords `Berlin`); pass it to `search_linkedin_people` or one of the other typed searches.
- **Answer received invitations.** `list_received_invitations`, then `respond_to_invitation` with `accept` or `decline`.
- **Invite the people who reacted to a post.** `get_post_engagers` with the post link, then `send_invitation` with `reactors[].linkedinUrl`.
- **Comment on someone's latest post.** `list_posts_by_author`, then `comment_on_post` with `posts[].socialId`.

## Limits

- Each LinkedIn account has a daily limit for each kind of action, and invitations also have a 7-day limit. `get_usage` shows what is used and what is left.
- `daily_limit_reached` or `weekly_limit_reached`: tell the user the time in `resetAt`. Do not retry.
- `note_limit_reached`: offer to send the invitation without a note. `note_too_long`: shorten the note to the number in `limit`.
- `linkedin_limit_reached` or `account_restricted`: LinkedIn itself refused. Stop and tell the user.
- `temporary_failure`: try once more.
- `upgrade_required`: the action belongs to the Agent plan. `not_available_on_this_account`: it needs Sales Navigator or Recruiter on the user's LinkedIn account.
- LinkedIn's User Agreement does not allow automated activity, and LinkedIn can restrict an account it decides is automated. Keep the numbers small and spread sends out; do not send a long list in one burst.

## More

Every action with its inputs: https://heyreagent.com/docs
