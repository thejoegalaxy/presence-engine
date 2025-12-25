# Sessions vs Users: Modeling Presence at the Correct Level

Presence systems fail most often because they are modeled at the **user level** instead of the **session level**.

This document explains why sessions are the correct primitive for presence, how user-level presence should be derived, and how this distinction prevents flapping, false offline states, and coordination errors.

---

## Why users are the wrong primitive

A user is an identity.  
Presence is behavior.

A single user may:

- open multiple browser tabs
- use multiple devices simultaneously
- background one session while another remains active
- reconnect after transient network loss

If presence is stored directly on the user, these behaviors overwrite each other.

Presence becomes unstable because **identity is not a runtime signal**.

---

## What a session represents

A session represents a single runtime instance:

- one browser tab
- one mobile app process
- one SDK client
- one connected environment

Each session has its own:

- lifecycle
- heartbeat
- activity state
- expiration window

Sessions are **observable and time-bound**.  
Users are not.

---

## Session-first presence (the correct model)

Presence must be tracked at the **session level**.

In a session-first model:

- sessions emit signals
- sessions transition through lifecycle states
- sessions expire independently

User presence is **derived** from session state, not written directly.

This allows presence to remain accurate under real-world behavior.

---

## Deriving user presence from sessions

User-level presence is computed by aggregating session states.

A simple precedence model works well:

- **active** beats idle
- **idle** beats away
- **away** beats stale
- **stale** beats offline

This ensures:

- an active session keeps the user active
- background or idle sessions do not override foreground activity
- closing one session does not collapse user presence

Derivation occurs at read time, not on write.

---

## Why derived presence matters

Derived presence provides:

- stability under rapid session changes
- tolerance to transient disconnects
- accurate availability indicators
- predictable coordination behavior

Storing user-level presence as a mutable flag cannot provide these properties.

---

## Interaction with other presence patterns

Session-first modeling integrates cleanly with:

- multi-tab presence
- idle detection
- reconnect handling
- grace windows
- lifecycle-aware state transitions

All of these patterns assume **sessions are the source of truth**.

---

## Common failure modes

Session vs user issues arise when systems:

- store `user.isOnline` directly
- overwrite user presence on any session change
- ignore multiple concurrent sessions
- tie presence to authentication events
- treat reconnects as new users

These failures stem from **identity-first modeling**.

---

## Summary

Presence is a runtime signal.  
Users are identities.

A correct presence system:

- models presence at the session level
- derives user presence from sessions
- avoids mutable user-level flags
- remains stable under real-world behavior

When sessions are the primitive, presence becomes truthful and coordination-ready.

---

## Related docs

- [Docs index](../index.md)
- [Online vs Offline Presence](../presence-online-offline.md)
- [Who’s Online API](../whos-online-api.md)
- [Multi-Tab Presence](./multi-tab.md)
- [Idle Detection](./idle-detection.md)
- [Reconnect Handling](./reconnects.md)
