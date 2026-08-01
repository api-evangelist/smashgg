---
name: Query start.gg tournament and event data
description: Read tournaments, events, entrants, and standings from the start.gg GraphQL API.
api: https://api.start.gg/gql/alpha
operations: [tournament, event, currentUser]
---

# Query start.gg tournament and event data

start.gg exposes a single GraphQL endpoint. Use this skill to read public
competitive-gaming tournament data.

## Auth
Send a personal access token (created in start.gg Developer Settings) on every
request:

```
Authorization: Bearer [token]
```

## Endpoint & transport
- POST to `https://api.start.gg/gql/alpha`
- `Content-Type: application/json`
- Body: `{ "query": "...", "variables": { ... } }`

## Steps
1. Resolve an event by its slug (`tournament/<tournament>/event/<event>`) with the
   `event` query:
   ```graphql
   query getEventId($slug: String) {
     event(slug: $slug) { id name }
   }
   ```
   variables: `{ "slug": "tournament/genesis-9-1/event/ultimate-singles" }`
2. From the event, page entrants and standings via the event's `entrants` and
   `standings` connections (Relay-style: use page/perPage inputs; read `nodes`
   and `pageInfo`).
3. For account-scoped data, use `currentUser` (OAuth token with `user.identity`
   scope; add `user.email` for the email field).

## Rules
- Rate limit: average <= 80 requests / 60 seconds.
- Complexity limit: a single request may return at most 1000 objects (including
  nested) - paginate rather than over-nesting.
- Errors surface as `{ "success": false, "message": "..." }` and/or the GraphQL
  `errors` array. See errors/smashgg-error-codes.yml.
