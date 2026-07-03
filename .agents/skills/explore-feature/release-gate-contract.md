# Release-gate authoring contract

When delegating to the `writing-e2e-tests` skill, hand it this contract verbatim. It exists
because `writing-e2e-tests` defaults to local-OSS output that ends at "green locally" — a gate
test must be **staging-ready** and stamped. This contract overrides those defaults.

## Output location & naming

- Write to `tests_end_to_end/e2e/tests/_release-gate/<lead-ticket>.spec.ts` (lowercased ticket
  key, e.g. `opik-7167.spec.ts`).
- If that file already exists → **append mode**: add a new `test()` (or extend the flow) inside
  the existing `test.describe`. Do not create a second file.

## Tags (on the `test.describe`)

```ts
{ tag: ['@release-gate', '@release-gate:<version>', '@<feature>'] }
```

- `@release-gate` — always.
- `@release-gate:<version>` — the stamp. `<version>` = `git show origin/main:version.txt` read at
  authoring time (see the stamp rules in SKILL.md). Never `@t1-smoke`/`@t2-cuj`/`@t3-nightly` —
  gate tests are not part of the curated tiers.
- `@<feature>` — the page-family tag matching the change (`@datasets`, `@trace-explore`, …).

**Append reconciliation:** if appending, the describe block keeps the **earliest un-shipped**
`@release-gate:<v>` across its tests, so the assembled feature gates the earliest-targeted release.

## Must be deployment-agnostic

- baseURL / auth / workspace come from `OPIK_DEPLOYMENT` (the suite's existing env config) — never
  hardcode `http://localhost:5173`.
- Self-seed all required state via the SDK/bridge (fixtures or `sdkClient`), and tear down what it
  creates. The same spec must run unchanged on staging.

## POM policy

- If a POM already covers the page (`pom/<name>.page.ts`), use/extend it.
- If not, prefer a small **inline** spec over authoring a full throwaway POM. QA POM-ifies on
  promotion. Keep the per-PR cost cheap.

## Conventions (inherited from the suite)

Follow `tests_end_to_end/e2e/` conventions: `test.step()` wrapping, UI-first assertions, selector
preference (testid → role → label → text → CSS), public-SDK-only seeding. The spec header is
minimal — ticket key + one-line scope only; no restated plan (it lives in Jira).

## Verification

Explore the live UI and run the spec **green against the dev's local stack** (see the local-run
gate in SKILL.md). Local-green is the PR gate; staging-green (CI, later) is the release gate.
