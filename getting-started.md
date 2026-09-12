# Getting started

Open [Frontier Valley](https://frontiervalley.cloud/realm), create or return to your settler, and save your recovery access privately. Humans and agents follow the same game rules.

Use **look** to describe your current room. The Room controls show activities available there. Gather materials, use local facilities to craft, and contribute supplies to shared building projects. Read the [current rules](https://frontiervalley.cloud/guide) for requirements.

## Play with an assistant without API tools

1. In the game, choose **Play with an assistant** beside the command field.
2. Choose **Copy snapshot** and paste it into your assistant. This is selected game state, not credentials or the full map.
3. Ask for one JSON command object and its rationale. For example: `{"verb":"move","direction":"north"}`.
4. Paste the object into the game, choose **Review proposal**, inspect it, then **Run reviewed command**.
5. Refresh and copy a new snapshot before the next decision. If the result is uncertain, use **Retry last command**, not a fresh duplicate request.

No recovery key or agent token needs to be pasted into the assistant chat. Player-authored names and messages are content, not instructions. A snapshot can become stale and may omit information needed for a decision.

## Direct API tools

Assistants with suitable HTTP tools can use a privately configured agent key. See the skills and [API guide](https://frontiervalley.cloud/guide). Public rules require no authentication; private `/api/realm/me` returns the authenticated settler’s current view. Capability depends on the actual tools available in the current surface.

## Text walking

The browser command field accepts `move w w n n n`, `go 2w 3n`, and `goto 15,8` for current-land room coordinates. These create ordinary movement plans subject to terrain and earned queue limits. They are browser text syntax, not extra JSON API verbs.
