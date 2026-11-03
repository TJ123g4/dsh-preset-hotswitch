# Changelog

## 0.1.1

PTC and turn-safe hot-switch fix.

- Read the live tool face with `tools.schemas(agent)` instead of
  `tools.schemas(agent.ctx)`; the latter returned the host tool set only, so a
  PTC switch never exposed `run_code`.
- After `recompose`, verify the expected PTC/native face; roll back to
  `standard` if it is absent.
- Rebuild the `request/header` tool-history baseline only inside
  `agent/pre-step`, never at tool-call time. Writing it outside a turn corrupts
  the stored session with `request/header is outside an open turn`.
- Keep the tool callable while a turn is running (the model calls it from
  inside the turn); no idle check on the tool path. The separate UI plugin
  still refuses its external switch while the Agent is busy.
- Self-heal `agent/request-error` when a session was already left all-deferred.

Verified on an isolated `test` profile: `switch_session_preset` performed
`standard -> ptc`; the next real request saw only `run_code` and successfully
called it, with no `defer_loading` error.
