# pi-continue

A Pi extension providing `/continue`: resume interrupted work without adding a user or custom message. Version 0.1.0 extracts the existing production source unchanged.

## Install

After the `v0.1.0` tag is published:

```sh
pi install git:github.com/MateoGreil/pi-continue@v0.1.0
```

**Do not load both this package and a local copy** such as `~/.pi/agent/extensions/continue/`. Disable or remove the old extension before installing; duplicate copies can register competing commands and stack prototype hooks.

## Use and behavior

Interrupt with Escape, then run `/continue` with no arguments while Pi is idle and a model is selected. It also works after reloading or reopening a saved interrupted session.

- Omits the final aborted/error assistant attempt from future model context while retaining it in raw session history.
- Preserves completed tool-call/result pairs and can resume from a user message or tool result.
- Runs the existing agent continuation, retry, and before-settle boundaries; Escape can cancel the resumed work.
- Refuses busy/streaming sessions, empty sessions, orphaned failed answers, arguments, and already completed assistant answers.

This does not undo or deduplicate external tool side effects. Review what already happened before resuming.

## Compatibility and internals

Verified against `@earendil-works/pi-coding-agent` **1.5.0**, using Node **22.22.3**; Node **>=22.22.0** is required by this package. No universal Pi compatibility is claimed. The `*` peer range means Pi supplies the SDK, not that every version is supported.

This extension patches the private `AgentSession.prototype._bindExtensionCore` method to capture sessions. It uses `agent.continue()` and these private session methods:

- `_omitRecoveryAttempt`, `_recordSelection`, `_handlePostAgentRun`, `_runBeforeSettleBoundary`
- `_finishCancelledRetry`, `_flushPendingBashMessages`, `_flushPendingCustomMessages`, `_emitAgentSettled`

It also writes `_isAgentRunActive`, `_agentRunAbortRequested`, `_failedResponse`, and `_runSystemPromptOptions`. The shutdown handler restores its prototype hook when it still owns it. Runtime shape checks reject missing prerequisites, but cannot detect every semantic change in Pi internals. Re-verify before using another Pi version.

## Tests

With the pinned development dependencies available:

```sh
npm test
```

Tests import SDK packages normally, use an offline stub model, and keep isolated session/resource fixtures under ignored `.worktrees/tests/`. They cover discovery, interrupted responses, tools, guards, cancellation, retry, reload, and saved sessions without calling a model service. Offline extraction checks can use an ignored `node_modules` symlink to an installed Pi 1.5.0 release.

## Distribution

Intended for a public Git repository and git-based Pi installation. `private: true` prevents accidental npm publication. `UNLICENSED` is intentional until a license is chosen; public visibility does not grant a reuse license.
