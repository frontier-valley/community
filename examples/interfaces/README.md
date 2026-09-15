# Make a Frontier Valley interface

1. Download `starter.html` from Community sharing. Edit its HTML, CSS and JavaScript. There is no framework or build step.
2. Host the file on a public HTTPS website.
3. Open Community in the official client, paste its URL, and preview it. The preview starts disconnected.
4. Click Connect to share this settler's visible state and permit game commands. Disconnect revokes that connection immediately.
5. Submit the hosted URL, a description, and optionally its source URL to community sharing. Submissions are community-made and unreviewed; authors can remove them. Report/mute controls also hide the author's submissions.

## Bridge version 1

The host sends `{type:'frontier:state',version:1,channel,state}` after explicit connection and on state refresh. This is the same player-visible state as GET `/api/realm/me`, with no private bookmark or bearer token. Display player text with `textContent`, never `innerHTML`.

Send `{type:'frontier:command',channel,id:crypto.randomUUID(),command:{verb:'gather',item:'wood'}}` to `parent.postMessage(message,'*')`. The random channel must match the most recently supplied one. The host verifies the sending frame, its connection and the channel. Wildcard messaging is needed because the sandboxed frame has an opaque origin. Check `event.source===parent` on incoming messages.

The host returns `{type:'frontier:result',channel,id,ok}`. Commands use the official client's normal queue, validation, error feedback and idempotent HTTP transport. A view should send one command at a time and wait for confirmation. An uncertain result must be resolved in the official client before sending another command. Do not retry automatically with a fresh request ID.

The sandbox permits scripts, but does not grant same-origin access, top navigation, popups or forms. Your code never receives the settler's private key. Connecting does share visible game state, including pack and personal history, with the chosen view; connect only to code you trust. A view can send normal game commands and the server applies the same rules as every other client.

## API reference and independent clients

Start with `/api/realm/rules?format=compact`, then fetch only the command or section you need. `/api/realm/rules` still provides the complete rules. Native or server-side clients can use the HTTP API with a securely held bearer token. Keep tokens out of URLs, submitted views, source code and logs. Browser clients hosted elsewhere should use the bridge; the game API deliberately rejects cross-origin requests. Never create a proxy that exposes your token publicly.

The game is server-authoritative. A custom view can draw rooms, people, jobs, queues, logistics, progression and wishes however it likes. It cannot invent inventory, bypass carrying or station limits, accelerate work, or overwrite snapshots. Always render the next server state after a command.

Building names are returned on `state.room.buildings[KIND].name` and in the current region's room data. Treat them as player-authored text. `state.storageAccess` says whether this settler owns the storehouse or may withdraw from it; the owner also receives the current trusted list. Use `name-building {structure,name}` and `set-storage-access {player,enabled}` through the same command bridge. These actions require the owner idle at the building. Trusted use does not grant permission to delegate access, rename, approve upgrades or unbuild. Revocation affects future withdrawals and material reservations, not already funded work.

## Worked spreadsheet example

`spreadsheet.html` is a complete, dependency-free community view with familiar spreadsheet chrome and switchable Sheets-style and Workbook-style colors. It presents only useful game facts: the settler, location, needs, current work, intention, pack and visible local projects. The names describe visual styles; the example is not affiliated with or endorsed by Google or Microsoft.

Choose **Use this view** in Community, or open `/realm?community=spreadsheet`, then choose **Connect**. The spreadsheet takes the place of the main game scene inside the normal browser window. **Preview** is a separate embedded inspection option. The page stays disconnected when opened directly because only the official client can provide its random bridge channel and current visible state. If the intention projection supplies an executable next command, the sheet previews that exact JSON and offers one explicit **Run next step** button. It offers explicit basic actions as well as the supplied next intention step. It never fetches the game API or receives credentials. While a command is pending it cannot be submitted again, and an unsuccessful result remains visibly unsuccessful.

The example checks the sending window, bridge version and current channel. It creates cells with `textContent`, keeps its optional theme preference behind a storage `try/catch`, and contains the wide grid in a horizontal scroller on small screens. These are useful defaults when adapting `starter.html` into a richer community interface.

## Quiet View worked example

`quiet.html` is an original, dependency-free room-first interface inspired by the restrained layout of [Corals by Antoinecarle](https://antoinecarle.itch.io/corals). No reference-game artwork or code is included. It places identity, location and current work above a compact view of known rooms; secondary information belongs in one drawer at a time.

Open [Quiet View in the game](https://frontiervalley.cloud/realm?community=quiet), then choose **Connect this view to my settler**. The matching [spreadsheet link](https://frontiervalley.cloud/realm?community=spreadsheet) opens the Sheets/Workbook example the same way. These links select the main interface, not a small preview or browser fullscreen. Use the normal Text, Graphics or Illustrated controls to return; leaving the community view revokes its connection. Directly opening or downloading either HTML file does not grant access to your settler. The official game supplies the connection after your consent.

The host disables Connect until the frame is loaded and sends `{type:'frontier:disconnected',version:1,channel}` when access is revoked. Views should immediately clear their channel and disable gameplay controls on that message. The host independently rejects commands from disconnected or replaced frames even if a view ignores the notification.

Treat a successful command result as confirmation of submission, not completed labor. Keep errors visible, wait for refreshed server state, and never skip an earlier blocked intention step just to find a later command. Basic actions remain available when there is no saved intention.