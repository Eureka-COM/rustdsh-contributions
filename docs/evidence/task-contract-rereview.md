# Task contract and approval foundation: re-review evidence

This opt-in foundation adds versioned task contracts, operation-bound human approvals,
and a deny-only DSH tool guard. It does not provide an OS enforcement adapter or
complete Issues #12, #29, #30, #32, or #33. The existing launcher and Harness agent
loop remain independently usable.

## Sources and verification

- Current main `1fdec5fc2bb77f76901726fe616396b5371016f0` is integrated in
  `3f1f7dc25910794de3557acf7303da494ac423e0`. Conflicts retain the current overview,
  typed question cards, answer application, budget/cost panels, navigation, and
  command palette. The contract/approval UI is a separate browser module, with
  its own expandable section and the existing literal-text regression test.
- `2b7862d4dce9a7c9cdc71b96a4b8693c4e860b2b` fixes long approval digests and source
  references extending the page beyond a 390 px viewport.
- `d6a94949e3cb4f69c5298bf0eb86e2098052a714` preserves the deny/audit result of a
  malformed null policy input when committing the merged event history, and
  updates two inherited inventory assertions for the twelve-tool catalog.
- Fresh Linux verification at `d6a9494`: Rust 68, plugin 13,
  dashboard 203, CLI regression 53, settings 20, and context 21 tests passed.
  `cargo fmt --check` and release Clippy with warnings denied passed.
- Windows Node 22 initially passed 200 of 203 integrated tests; the three failures
  above were fixed, and all 19 tests in their three affected files then passed.
  Final all-suite Windows validation is provided by the PR's current-head CI.
- Windows Chrome exercised the current production UI against the real Project
  HTTP server and persistent state in a disposable fixture at 1440 px and 390 px.
  The before screenshot serves main's UI against the same fixture/backend data;
  it compares the UI, rather than a historical server deployment.
- Earlier evidence at `ec1f0cf0c71881e4730a263f9dab4fe007fb0ce8` includes two passing
  tests with the real DSH ToolRuntime 0.2.0-rc.2. The six boundary modules
  (`contracts`, `approvals`, `policy`, `enforcement`, `dsh-guard`, and `provenance`)
  are byte-identical to that tested source. Those SDK tests were not rerun during
  this re-review; the fresh 29 dashboard tests and browser checks are separate.

## Browser results

The [sanitized result record](task-contract-browser.json) includes both viewport
cases. Credentials were temporary and are absent from the evidence.

- Human grant and revoke persisted through the actual browser controls.
- An altered operation body was rejected with `approval_operation_changed`.
- Replacing contract version 1 with version 2 invalidated the old grant with
  `contract_version_changed`; revocation returned `approval_revoked`.
- An unsent question reply survived state updates. Answering a question does not
  grant permission to run an operation.
- External markup-like text remained literal text; no injected image executed.
- No page errors or horizontal page overflow occurred at either width.
- A valid approval still left execution on hold. Worker startup returned
  `enforcement_adapter_unavailable`, execution stayed `not_started`, and zero
  attempts were consumed. No model, real tool, or paid API ran.

## UI evidence

Before, using main's UI:

![Main dashboard before the contract UI](../screenshots/task-contract-before.png)

After, with a pending request and versioned contract:

![Contract and approval dashboard](../screenshots/task-contract-after.png)

Grant and revoke through the production UI:

![Pending, granted, and revoked request flow](../screenshots/task-contract-flow.gif)

Revoked state and mobile layout:

![Revoked approval](../screenshots/task-contract-revoked.png)
![390 px mobile layout](../screenshots/task-contract-mobile.png)

## Remaining boundary

This is ready for review as a bounded, opt-in foundation. OS/container enforcement,
automatic DSH provenance capture, launcher/startup reads, direct backends, LLM
requests, and plugin unloading are outside its protected tool boundary. The guard
has no allow/claim path that would execute a tool without a verified adapter.
These checks do not establish production adoption or full sandbox protection.
