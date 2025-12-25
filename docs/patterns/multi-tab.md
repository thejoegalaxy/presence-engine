# Multi-Tab Presence: Avoiding User-Level Flapping

Modern web applications routinely run in multiple tabs, windows, and devices at the same time.  
A correct presence system must treat this as normal behavior, not an edge case.

This document explains why **multi-tab presence breaks naïve implementations** and how a **session-first model** avoids user-level flapping and false offline states.

---

## The multi-tab problem

A single user may:

- open multiple tabs of the same application
- background one tab while another remains active
- close a tab while another is still open
- switch devices during an active session

If presence is stored at the **user level**, these actions create ambiguity.

A single tab closing should not mark a user offline.  
A backgrounded tab should not override an active one.

---

## Why user-level presence fails in multi-tab scenarios

A common pattern looks like this:

- tab connects → user marked online
- tab disconnects → user marked offline

With multiple tabs, this produces:

- rapid online/offline flapping
- incorrect availability indicators
- misleading downstream systems
- loss of trust in presence signals

The problem is not WebSockets or Socket.IO.  
The problem is **modeling presence at the wrong level**.

---

## Session-first presence (the correct model)

Presence must be tracked per **session**, not per user.

A session represents:

- a single browser tab
- a single mobile app instance
- a single SDK runtime

Each session has its own:

- lifecycle
- heartbeat
- expiration

Users are **derived aggregates of sessions**, not primary presence entities.

---

## Deriving user presence from sessions

User-level presence is computed from active sessions using precedence rules.

A simple and effective rule set:

- **active** beats idle
- **idle** beats away
- **away** beats stale
- **stale** beats offline

This ensures that:

- an active tab keeps the user active
- background tabs do not override foreground activity
- closing one tab does not collapse user presence

Presence is derived at read time, not written as a mutable user flag.

---

## Handling tab lifecycle events

Tabs transition through states independently:

- foreground → background
- background → foreground
- active → idle
- connected → expired

Presence systems should:

- accept these transitions as signals
- update session state
- recompute user presence without side effects

A tab going idle is **not** a user leaving.  
It is one signal among many.

---

## Common failure modes

Multi-tab issues typically arise from:

- storing `user.isOnline` as a boolean
- overwriting user presence on any disconnect
- ignoring background visibility state
- expiring presence immediately on socket close
- treating reconnects as new users

These failures are symptoms of **user-first modeling**.

---

## Summary

Multi-tab behavior is normal.

A correct presence system:

- treats tabs as independent sessions
- derives user presence from session state
- avoids writing user-level online flags
- remains stable under rapid tab changes

When modeled correctly, multi-tab presence becomes predictable and trustworthy.

---

## Related docs

- [Docs index](../index.md)
- [Online vs Offline Presence](../presence-online-offline.md)
- [Who’s Online API](../whos-online-api.md)
- [Presence with Socket.IO](../presence-socket-io.md)
