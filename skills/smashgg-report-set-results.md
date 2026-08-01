---
name: Report a set result on start.gg
description: Report the winner (and optional per-game data) of a bracket set via the start.gg GraphQL API.
api: https://api.start.gg/gql/alpha
operations: [reportBracketSet]
---

# Report a set result on start.gg

Use this skill to report match/set outcomes for tournaments you manage.

## Auth & scope
- Requires a token with reporting access. Via OAuth this is the
  `tournament.reporter` scope (see scopes/smashgg-scopes.yml).
- Send `Authorization: Bearer [token]`.

## Steps
1. Identify the `setId` you are reporting and the `winnerId` (entrant id).
2. Call the `reportBracketSet` mutation:
   ```graphql
   mutation reportSet($setId: ID!, $winnerId: ID!) {
     reportBracketSet(setId: $setId, winnerId: $winnerId) { id state }
   }
   ```
   variables: `{ "setId": 65089253, "winnerId": 14259653 }`
3. To report per-game detail (scores, stages, character selections), pass
   `gameData: [BracketSetGameDataInput]`:
   ```graphql
   mutation reportSet($setId: ID!, $winnerId: ID!, $gameData: [BracketSetGameDataInput]) {
     reportBracketSet(setId: $setId, winnerId: $winnerId, gameData: $gameData) { id state }
   }
   ```
4. The response returns the affected sets with their new `state` (progression
   updates neighboring sets automatically).

## Rules
- Mutations are NOT idempotent - do not blindly retry a `reportBracketSet` call;
  re-query the set state first. See conventions/smashgg-conventions.yml.
- Respect the 80 req / 60s and 1000-object complexity limits.
