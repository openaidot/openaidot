# Memory sync — how Dot's understanding stays current

Goal: every meaningful thing the Dot learns is committed to this public repo, without giving the Dot a GitHub credential.

## Recommended path: hub tool + deploy key

1. The Dot calls a hub MCP tool `memory_commit({ summary, notes })`.
2. The hub:
   - validates the JSON schema;
   - redacts emails, QQ numbers, IPs, tokens, and private paths;
   - writes `memory/state.json` and `memory/notes/<YYYY-MM-DD>.md`;
   - runs `git add memory && git commit -m "memory: <summary>" && git push`.
3. The repo uses a **deploy key** scoped to this repository; the key lives on the VPS, not in the Dot.
4. A GitHub Action validates the schema and rejects secrets on every push.

## Schema

```json
{
  "updated_at": "2026-10-29T00:00:00Z",
  "summary": "one line",
  "open_tasks": ["..."],
  "notes": [
    {"id": "n-001", "date": "2026-10-29", "topic": "group", "text": "...", "source": "qq:<message_id>"}
  ]
}
```

## Cadence

- After each significant group interaction (mention, task, decision).
- At least once every 30 minutes while the bot is active.
- A daily rollup at 04:00 local time.

## Public-safety gate

Never commit:

- credentials, tokens, cookies, private keys;
- other people's personal data (emails, phone numbers, addresses);
- private chat logs that are not needed for the deployment story.

The hub redactor is the first gate; the GitHub Action is the second.

## Alternative: GitHub Contents API

If the hub cannot run git, give the Dot a fine-grained PAT limited to this repo and have it call `PUT /repos/<owner>/<repo>/contents/memory/...`. This is simpler but puts a credential in the Dot's environment; prefer the deploy-key path.
