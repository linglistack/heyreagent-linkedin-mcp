# HeyReagent: a LinkedIn MCP server for your own account

HeyReagent is a hosted MCP server and API for your own LinkedIn account. You sign in once, connect LinkedIn, and give your AI app one address. From then on it can read your inbox, send messages and invitations, search people, and work with posts.

**The MCP address:** `https://api.heyreagent.com/mcp`

It is an address, so there is nothing to install or keep running, and it works in apps that live in a browser, such as Claude and ChatGPT on the web.

HeyReagent is not made by, affiliated with or endorsed by LinkedIn, and LinkedIn has not published an MCP server of its own. It is an independent service that acts on your own LinkedIn account, at your direction.

- The site: https://heyreagent.com/linkedin-mcp?utm_source=github&utm_medium=readme
- The docs, with every action: https://heyreagent.com/docs?utm_source=github&utm_medium=readme
- The whole API as an OpenAPI 3.1 file: https://heyreagent.com/openapi.json

This repository holds the steps to connect an AI app, and two n8n workflows. The server is hosted; its code is not here.

## Before you connect

1. Sign in at [heyreagent.com](https://heyreagent.com/signin?utm_source=github&utm_medium=readme) and connect your own LinkedIn account. It is free to start.
2. Claude and ChatGPT sign in to your account and need nothing more. For the others, make a key on the thread page: Menu, then Developers. It is shown once.

## Connect your AI app

### Claude

Signs in to your account. No key.

1. Open Customize, then Connectors.
2. Click + Add, then Add custom connector.
3. Type a name, paste the address, and click Continue.
4. Keep the sign-in settings Claude found, continue, and click Add. Sign in to HeyReagent when Claude asks.
5. In a chat, press + at the lower left, then Connectors, to switch it on.

### ChatGPT

Signs in to your account. No key.

1. Open Settings, then Security and login, and turn on Developer mode.
2. Go to ChatGPT Plugins, select the plus button, type a name and a description, enter the address, and create the connection.
3. Sign in to HeyReagent when ChatGPT asks.
4. In a chat, choose Developer mode from the plus menu and select it.

### Muse

Uses your key.

1. Send Muse the message below, as it is.
2. Muse answers with a card for the connector. Press Connect on it, then Continue, and paste your key in the form. The key goes there, not in the chat.
3. Muse asks whether it may share information with api.heyreagent.com. “Always allow this site” stops it asking before every request. “Allow once” allows the next one only.
4. Muse lists the actions and runs the check. Then ask it for what you want.

```
Create a custom connector called HeyReagent for my LinkedIn. It is a remote MCP server over streamable HTTP at https://api.heyreagent.com/mcp. Every request needs the header "Authorization: Bearer" followed by my key. Ask me for the key in your secure form, not in this chat. Then list its tools and run get_my_profile to check that it works.
```

### Claude Code

Uses your key.

1. Run this in a terminal, with your own key where it says YOUR_KEY.

```
claude mcp add --transport http heyreagent https://api.heyreagent.com/mcp \
  --header "Authorization: Bearer YOUR_KEY"
```

### Cursor

Uses your key.

1. Put this in .cursor/mcp.json in your project, or in ~/.cursor/mcp.json to have it everywhere, with your own key where it says YOUR_KEY.

```
{
  "mcpServers": {
    "heyreagent": {
      "url": "https://api.heyreagent.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_KEY"
      }
    }
  }
}
```

### n8n

Uses your key.

1. Add an MCP Client Tool node to your agent.
2. Endpoint: the address above. Server Transport: HTTP Streamable.
3. Authentication: Bearer Auth, with your key.

## What it can do

There are 97 actions (counted on 6 October 2026): Messages (27), Search (17), Posts (11), Jobs (10), Network (9), Profile (5), InMail (5), Recruiter (4), Insight (4), Account (4) and Sales Navigator (1). A few of them: `who_is_waiting_on_me`, `list_conversations`, `send_message`, `send_invitation`, `search_people`, `get_recent_posts`, `comment_on_post`.

[Every action, with its inputs and what comes back](https://heyreagent.com/docs?utm_source=github&utm_medium=readme#actions).

The Sales Navigator, Recruiter and job-posting actions run on your own LinkedIn account and need that product on it.

## n8n workflows

Two workflows that call the API from n8n's HTTP Request node. In n8n: the three dots at the top right, then Import from File (or Import from URL with the file's raw address). Each HTTP Request node needs a Bearer Auth credential holding your key; no key is written in the files.

| File | What it does |
|---|---|
| [`n8n/find-people-and-invite.json`](n8n/find-people-and-invite.json) | Searches LinkedIn people by plain words and a place, and sends each one a connection invitation with your note |
| [`n8n/invite-post-reactors.json`](n8n/invite-post-reactors.json) | Takes the link of a LinkedIn post and sends a connection invitation to the people who reacted to it |

The guide: https://heyreagent.com/n8n-linkedin?utm_source=github&utm_medium=readme

## Plans

| | |
|---|---|
| Free, $0 | Every Connect action. LinkedIn is disconnected after a while without use, so it is for working live. |
| Connect, $29.99 a month | The same actions with LinkedIn kept connected, so scheduled workflows run. |
| Agent, $499 a month | It runs LinkedIn for you: answers your inbox in your voice, follows up, and finds who to talk to. You approve what it sends. |

## Limits, and your LinkedIn account

Each LinkedIn account has a daily limit for each kind of action; an action past its limit is refused, with the time it resets. Reading your conversations is not limited. [All the limits](https://heyreagent.com/docs?utm_source=github&utm_medium=readme#limits).

LinkedIn's User Agreement does not allow automated activity, and LinkedIn can limit or restrict an account that it decides is automated. Keep the numbers small, and spread what you do over the day.

## Questions

hello@heyreagent.com
