<p align="center">
  <img src="icon-512.png" width="96" height="96" alt="Tavern">
</p>

# Tavern MCP server

Your tabletop RPG campaigns, as tools. **[Tavern](https://gettavern.app)** keeps a TTRPG group's campaign in one place: dated sessions with everybody's notes, a wiki of the people, places and things in them, maps with pins, character sheets with live hit points and spell slots, a combat tracker, and each player's private notes. This server lets Claude, Cursor, VS Code or any MCP client work on all of it, acting as you at the tables you already sit at.

This repository holds the listing only: this README, the registry entry, the Gemini CLI extension manifest and the icon. The server is hosted by Tavern, so there is nothing to install or run.

| | |
| --- | --- |
| **Server address** | `https://gettavern.app/mcp` |
| **Transport** | Streamable HTTP (remote, stateless) |
| **Sign-in** | OAuth 2.1 with PKCE, or a personal API key |
| **Registry name** | `io.github.jakehandy/tavern` |
| **Docs** | [gettavern.app/developers](https://gettavern.app/developers) |

## What you can ask it

- "Recap Thursday's session from everybody's notes."
- "Start a wiki page for the ferryman at Hollowmere and link him in tonight's notes."
- "Ilsa takes 9 and she's prone."
- "Which threads from the last three sessions have we dropped?"
- "Set up a fight: the party against the three toll-bridge bandits."
- "What has my character got left?"

You need a free Tavern account and a campaign. Sign up at [gettavern.app/signin](https://gettavern.app/signin).

## Connect it

### Claude

In Settings, under Connectors, add a custom connector and paste `https://gettavern.app/mcp`. Claude opens a Tavern page asking you to approve it.

### Claude Code

```sh
claude mcp add --transport http tavern https://gettavern.app/mcp
```

Then run `/mcp` inside a session to sign in.

### Cursor

Add it to `~/.cursor/mcp.json`. Cursor asks you to sign in the first time it connects.

```json
{
  "mcpServers": {
    "tavern": {
      "url": "https://gettavern.app/mcp"
    }
  }
}
```

### VS Code

Add it to `.vscode/mcp.json` in a workspace, or to your user settings.

```json
{
  "servers": {
    "tavern": {
      "type": "http",
      "url": "https://gettavern.app/mcp"
    }
  }
}
```

### Gemini CLI

This repository is a Gemini CLI extension. Install it, then run `/mcp auth tavern` inside Gemini CLI to sign in.

```sh
gemini extensions install https://github.com/jakehandy/tavern-mcp
```

### Anything else

Any client that speaks Streamable HTTP finds the sign-in on its own. A client that only takes a header can send a personal API key instead (make one in Tavern under Settings, API and assistants):

```json
{
  "url": "https://gettavern.app/mcp",
  "headers": { "Authorization": "Bearer tvn_your_key" }
}
```

## Signing in

The first time a client connects, the server answers `401` with a pointer to its sign-in, and the client opens Tavern's own consent page. You sign in there, see what the app is asking for, and may grant less than it asked.

- **OAuth 2.1 for public clients**: PKCE (S256), no client secret. A client registers itself, either through dynamic client registration (RFC 7591) or by being its own Client ID Metadata Document. Access tokens last an hour; refresh tokens rotate on every use.
- **Scopes stack**: `read`, then `write`, then `delete`. A client only sees the tools its scopes allow.
- **Metadata**: [`/.well-known/oauth-protected-resource/mcp`](https://gettavern.app/.well-known/oauth-protected-resource/mcp) and [`/.well-known/oauth-authorization-server`](https://gettavern.app/.well-known/oauth-authorization-server). The full story is at [gettavern.app/auth.md](https://gettavern.app/auth.md).
- **Revoking**: every key and connected app is listed under Settings, API and assistants, with when it was last used, and goes the moment you remove it.

A connected app is you and no more. It reaches the tables you sit at with the seat you hold at each, so a player's connection can't do the GM's job, and nobody's private notes are served to anyone but their author.

## Tools

117 tools in fourteen groups as of November 2026. The server is the source of truth: your client lists exactly the tools your sign-in allows, and [gettavern.app/developers](https://gettavern.app/developers#reference) always shows the current catalog with every argument. Every tool carries a title and read-only or destructive hints.

Campaigns are named by slug, sessions by their play date (`YYYY-MM-DD`), and wiki pages and characters by slug. Start with `list_campaigns`.

To load fewer tools, add `?toolsets=` to the address with any of the group keys (`account`, `campaigns`, `sessions`, `wiki`, `notes`, `messages`, `map`, `characters`, `builder`, `combat`, `lorekeeper`, `memorial`, `dice`, `updates`) or the preset `core`. For example `https://gettavern.app/mcp?toolsets=core,characters`.

### You

Who the credential is, your public profile, your invitations and feedback.

| Tool | What it does | Scope |
| --- | --- | --- |
| `whoami` | Who am I | read |
| `list_campaigns` | List my campaigns | read |
| `respond_to_invitation` | Answer an invitation | write |
| `get_profile_settings` | Get my profile settings | read |
| `update_profile_settings` | Change my profile settings | write |
| `get_subscription` | Get my subscription | read |
| `get_public_profile` | Read a public profile | read |
| `send_feedback` | Send feedback | write |
| `list_my_feedback` | List my feedback | read |

### Campaigns and seats

Make a table, fill its seats, change its look, leave one.

| Tool | What it does | Scope |
| --- | --- | --- |
| `create_campaign` | Create a campaign | write |
| `get_campaign` | Get a campaign | read |
| `update_campaign_look` | Change a campaign's look (GM only) | write |
| `list_seats` | List the seats | read |
| `add_seat` | Add a seat (owner only) | write |
| `remove_seat` | Remove a seat (owner only) | delete |
| `get_my_seat` | Get my seat | read |
| `update_my_seat` | Change my seat | write |
| `leave_campaign` | Leave a campaign | delete |

### Session journal

Play dates and the notes everybody writes on them.

| Tool | What it does | Scope |
| --- | --- | --- |
| `list_sessions` | List sessions | read |
| `get_session` | Read a session | read |
| `create_session` | Log a session (GM only) | write |
| `update_session` | Rename or move a session (GM only) | write |
| `add_session_note` | Add a note to a session | write |
| `update_session_note` | Edit a session note | write |
| `delete_session_note` | Delete a session note | delete |

### Campaign wiki

Pages for every person, place and thing, their history, and search across the lot.

| Tool | What it does | Scope |
| --- | --- | --- |
| `list_wiki_pages` | List wiki pages | read |
| `get_wiki_page` | Read a wiki page | read |
| `get_wiki_page_history` | Read a page's history | read |
| `create_wiki_page` | Create a wiki page | write |
| `update_wiki_page` | Edit a wiki page | write |
| `delete_wiki_page` | Delete a wiki page (GM only) | delete |
| `link_wiki_page_to_seat` | Mark a page as somebody's character | write |
| `search_campaign` | Search a campaign | read |

### My Notes

Your own sealed notes, and everything you have written at a table.

| Tool | What it does | Scope |
| --- | --- | --- |
| `list_my_notes` | List my private notes | read |
| `create_my_note` | Write a private note | write |
| `update_my_note` | Edit a private note | write |
| `delete_my_note` | Delete a private note | delete |
| `get_my_note_history` | Read a private note's history | read |
| `list_everything_i_wrote` | Everything I wrote here | read |

### Messages

Direct conversations between the people at one table, and the GM's thread to all of them.

| Tool | What it does | Scope |
| --- | --- | --- |
| `list_conversations` | List my conversations | read |
| `start_conversation` | Start a conversation | write |
| `read_conversation` | Read a conversation | read |
| `send_message` | Send a message | write |
| `mark_conversation_read` | Mark a conversation read | write |
| `delete_message` | Delete a message | delete |

### Map

The table's maps and the pins on them, shared and private.

| Tool | What it does | Scope |
| --- | --- | --- |
| `list_maps` | List maps | read |
| `list_pins` | List a map's pins | read |
| `list_my_private_pins` | List my private pins | read |
| `create_pin` | Drop a pin | write |
| `update_pin` | Move or edit a pin | write |
| `delete_pin` | Delete a pin | delete |
| `update_map` | Rename or reorder a map (GM only) | write |
| `delete_map` | Take a map down (GM only) | delete |

### Characters

Sheets, hit points, conditions, spell slots, items, homebrew and companions.

| Tool | What it does | Scope |
| --- | --- | --- |
| `list_characters` | List characters | read |
| `get_character` | Read a character | read |
| `get_party` | The party at a glance (GM only) | read |
| `update_character_state` | Change hit points, conditions or slots | write |
| `set_character_overlay` | Replace what has been spent | write |
| `add_character_from_beyond` | Add a character from Beyond | write |
| `sync_character` | Sync a character from Beyond | write |
| `set_character_on_hud` | Show or hide a character on the Master HUD (GM only) | write |
| `play_character` | Make a character my primary (holder only) | write |
| `mark_character_fallen` | Mark a character as fallen | write |
| `revive_character` | Bring a character back | write |
| `delete_character` | Delete a character | delete |
| `assign_character` | Hand a character to a seat (GM only) | write |
| `set_character_portrait` | Set a character's portrait | write |
| `add_custom_item` | Add a custom item | write |
| `update_custom_item` | Edit a custom item | write |
| `delete_custom_item` | Delete a custom item | delete |
| `add_homebrew_box` | Add a homebrew card | write |
| `update_homebrew_box` | Edit a homebrew card | write |
| `delete_homebrew_box` | Delete a homebrew card | delete |
| `add_companion` | Add a companion | write |
| `update_companion` | Edit a companion | write |
| `set_companion_state` | Change a companion's hit points | write |
| `delete_companion` | Delete a companion | delete |

### Character builder

Make a character from scratch, or design the sheet itself.

| Tool | What it does | Scope |
| --- | --- | --- |
| `create_custom_character` | Make a character here | write |
| `update_custom_sheet` | Edit a character made here | write |
| `list_character_drafts` | List my half-made characters | read |
| `start_character_draft` | Start a character draft | write |
| `get_character_draft` | Read a character draft | read |
| `update_character_draft` | Save a character draft | write |
| `finish_character_draft` | Finish a character draft | write |
| `delete_character_draft` | Throw a draft away | delete |
| `list_sheet_frames` | List the table's sheet designs | read |
| `save_sheet_frame` | Keep a sheet design at the table | write |
| `delete_sheet_frame` | Take a sheet design off the table | delete |

### Combat Tracker

Run a battle: the roster, initiative, turns, health and the summary.

| Tool | What it does | Scope |
| --- | --- | --- |
| `get_combat_roster` | Who can join a battle (GM only) | read |
| `list_battles` | List battles (GM only) | read |
| `create_battle` | Set up a battle (GM only) | write |
| `get_battle` | Read a battle (GM only) | read |
| `update_battle` | Rename a battle or set the turn (GM only) | write |
| `add_participant` | Put somebody in a battle (GM only) | write |
| `update_participant` | Change somebody in a battle (GM only) | write |
| `change_participant_health` | Damage or heal somebody in a battle (GM only) | write |
| `remove_participant` | Take somebody out of a battle (GM only) | delete |
| `start_battle` | Start a battle (GM only) | write |
| `next_turn` | Next turn (GM only) | write |
| `end_battle` | End a battle (GM only) | write |
| `delete_battle` | Delete a battle (GM only) | delete |

### Lorekeeper

Hand a session's notes to Lorekeeper and watch the write-up land.

| Tool | What it does | Scope |
| --- | --- | --- |
| `list_lorekeeper_jobs` | Lorekeeper's queue (GM only) | read |
| `queue_session_for_lorekeeper` | Hand session notes to Lorekeeper (GM only) | write |
| `cancel_lorekeeper_job` | Take notes back from Lorekeeper (GM only) | delete |

### Memorial Pages

Close the book on a campaign and read its memorial.

| Tool | What it does | Scope |
| --- | --- | --- |
| `get_memorial` | Read a campaign's memorial | read |
| `complete_campaign` | Complete a campaign (GM only) | write |
| `reopen_campaign` | Reopen a campaign (GM only) | write |
| `set_memorial_public` | Share or hide the memorial (GM only) | write |

### Dice

Roll any dice on the server, and choose how your dice look in the app.

| Tool | What it does | Scope |
| --- | --- | --- |
| `roll_dice` | Roll dice | read |
| `get_dice_style` | My dice style | read |
| `set_dice_style` | Change my dice style | write |

### Notifications and progress

What's new at your tables, and how far you've come.

| Tool | What it does | Scope |
| --- | --- | --- |
| `list_notifications` | List my notifications | read |
| `count_unread_notifications` | Count unread notifications | read |
| `mark_notifications_read` | Mark notifications read | write |
| `delete_notification` | Clear a notification | delete |
| `get_my_progress` | My Tavern Level and achievements | read |
| `list_achievements` | Every achievement | read |

## Also available as a REST API

The same catalog is a REST API at `https://gettavern.app/api/v1`, described at [/openapi.json](https://gettavern.app/openapi.json) and in prose at [/api-docs.md](https://gettavern.app/api-docs.md).

```sh
curl https://gettavern.app/api/v1/campaigns -H "Authorization: Bearer tvn_your_key"
```

## Links

- Developers: [gettavern.app/developers](https://gettavern.app/developers)
- Privacy Policy: [gettavern.app/privacy](https://gettavern.app/privacy)
- Terms of Service: [gettavern.app/terms](https://gettavern.app/terms)
- Support: [support@gettavern.app](mailto:support@gettavern.app)

## License

The files in this repository are MIT licensed (see [LICENSE](LICENSE)). The Tavern name and mark are not covered by it. The Tavern service itself is governed by its [Terms of Service](https://gettavern.app/terms).
