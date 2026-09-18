# petstore-api-source

Simulates a dev team's repo: it owns the API contract (`openapi.yaml`) and
nothing else. It doesn't know or care who consumes that contract — it just
announces "the spec changed" whenever `main` gets a new version of it.

## Role in the demo

This repo is the **source of truth**. When `openapi.yaml` changes on `main`,
the workflow in `.github/workflows/notify-docs-repo.yml` fires a
`repository_dispatch` event at the `petstore-docs-portal` repo, carrying the
commit SHA and message. That's the entire job of this repo — one push, one
event, no knowledge of what happens downstream.

See the top-level `DEMO_SCRIPT.md` (one level up) for the full live-demo
walkthrough across both repos.

## One-line demo edit

To trigger the sync visibly, make an edit to `openapi.yaml` that's obviously
visible once the docs site regenerates. Either of these works well:

**Option A — change the Pet status enum description.** Find the `Pet`
schema's `status` property and edit its `description` field, e.g. change:

```yaml
status:
  type: string
  description: pet status in the store
```

to:

```yaml
status:
  type: string
  description: pet status in the store (available, pending, or sold — updated for the v2 lifecycle)
```

**Option B — add a query parameter to `GET /pet/findByStatus`.** Add a new
entry to that operation's `parameters` list, e.g.:

```yaml
- name: limit
  in: query
  description: Maximum number of results to return
  required: false
  schema:
    type: integer
    format: int32
```

Either edit regenerates a visibly different API reference page once it
reaches the docs portal.

## Setup checklist (do this before the demo)

- [ ] Create the real GitHub repo and push this local repo to it.
- [ ] Create a PAT (classic, `repo` scope, or a fine-grained token scoped to
      the docs portal repo) and add it to this repo's secrets as
      `DOCS_REPO_DISPATCH_TOKEN`.
- [ ] Edit `.github/workflows/notify-docs-repo.yml` and replace
      `OWNER/petstore-docs-portal` with the real owner/repo name.
