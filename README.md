# LedgerMem for Make.com (Integromat)

Source for the LedgerMem custom app on [Make](https://make.com). This repo contains the JSON manifest, connection, modules, webhook receiver, and a sample scenario template.

## Repo layout

```
app.json                       Top-level Make app definition (base URL, headers, common errors)
connections/apikey.json        API-key + workspace-id connection
modules/
  newMemory.json               Polling trigger — watches GET /memories
  searchMemory.json            Search action — POST /search, returns hits[]
  addMemory.json               Create action — POST /memories
  updateMemory.json            Update action — PATCH /memories/:id
  deleteMemory.json            Delete action — DELETE /memories/:id
webhooks/newMemoryHook.json    Instant trigger receiver (configurable webhook)
scenarios/slack-to-ledgermem.json   Sample scenario: Slack → sentiment → LedgerMem
```

## Connection

API key + workspace id. Both ship as headers (`Authorization: Bearer …`, `x-workspace-id: …`). The connection test calls `POST /v1/search` with a 1-result probe.

## Submission to Make App Studio

Custom apps for Make are authored in the Make App Studio UI; the JSON in this repo is the source of truth used to populate it.

1. Create a new private app at https://www.make.com/en/developer-hub.
2. Set the base URL to `https://api.proofly.dev/v1` and paste `app.json` → `base.headers` into the Base section.
3. Create a new connection of type `API Key`. Paste `connections/apikey.json` parameters and communication blocks.
4. For each module in `modules/`, create a matching module in App Studio (Action / Search / Trigger) and paste the JSON.
5. Add the webhook from `webhooks/newMemoryHook.json` once instant events are live on the LedgerMem side.
6. Use `scenarios/slack-to-ledgermem.json` as a template — import via Scenarios → Import Blueprint.
7. To list publicly: open the app → "Submit for review". Make's review team validates the manifest and connection test.

## Modules

| Module          | Type            | Endpoint                  |
| --------------- | --------------- | ------------------------- |
| `newMemory`     | Polling trigger | `GET /v1/memories`        |
| `searchMemory`  | Search action   | `POST /v1/search`         |
| `addMemory`     | Create action   | `POST /v1/memories`       |
| `updateMemory`  | Update action   | `PATCH /v1/memories/:id`  |
| `deleteMemory`  | Delete action   | `DELETE /v1/memories/:id` |
| `newMemoryHook` | Instant webhook | inbound from LedgerMem    |

## CI

`.github/workflows/ci.yml` validates every JSON file with `jq` so a malformed module fails fast.

## License

MIT — see `LICENSE`.
