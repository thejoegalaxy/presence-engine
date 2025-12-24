# Designing a Who’s Online API (Users vs Sessions)

“Who’s online?” looks simple.  
In real systems, it is one of the most commonly mis-modeled APIs.

This guide explains how to design a reliable who’s online API by separating **sessions from users**, deriving presence from signals, and avoiding misleading counts.

---

## Why naïve “online users” APIs fail

Most implementations start with a user-level flag:

```js
user.isOnline = true;
```

This approach fails immediately.

A single user can:
- open multiple tabs
- use multiple devices
- background a session
- lose network connectivity transiently

If you toggle a user-level boolean on connect and disconnect, your API will:
- undercount active users
- overcount availability
- flap between states
- mislead downstream systems

**Online is not a property of a user.**  
It is an **emergent property of active sessions over time**.

---

## Sessions vs users (the core distinction)

A **session** represents a single runtime instance:
- a browser tab
- a mobile app instance
- an SDK client

A **user** may have many sessions.

Correct presence modeling always starts with sessions:

```text
user
 ├─ session A (active)
 ├─ session B (idle)
 └─ session C (stale)
```

User-level presence must be **derived**, not stored.

---

## What a who’s online API should return

Instead of returning a flat list of users, a reliable API exposes:
- active sessions
- derived user presence
- timestamps
- confidence indicators

Example conceptual response:

```json
{
  "activeSessions": 12,
  "users": [
    {
      "userId": "u123",
      "derivedState": "active",
      "lastSeenAt": "2025-02-10T12:01:30Z",
      "sessionCount": 2
    }
  ]
}
```

The key is transparency:
- expose **when** presence was last observed
- do not hide uncertainty behind booleans

---

## Concurrency vs totals

A common mistake is confusing:
- total users
- currently active users
- concurrent sessions

Concurrency is what matters for:
- capacity planning
- coordination
- real-time UX
- A/V readiness

A who’s online API should make concurrency explicit.

---

## Deriving user presence safely

User-level presence is derived from session states using precedence rules:

- **active** beats idle  
- **idle** beats away  
- **away** beats stale  
- **stale** beats offline  

This prevents:
- marking users offline when one tab closes
- flapping caused by transient disconnects

Presence is computed at read time, not written as state.

---

## Avoiding misleading counts

Do **not**:
- store `onlineUserCount` in a database
- increment/decrement counters on connect/disconnect
- broadcast counts without timestamps

Counts without context are lies.

Instead:
- derive counts from live session state
- expose time windows
- allow consumers to choose thresholds

---

## Who consumes a who’s online API

A reliable who’s online API enables:
- activity indicators
- readiness gates
- call initiation logic
- live dashboards
- coordination workflows

It is **not** just a UI convenience.  
It is a coordination primitive.

---

## Summary

A correct who’s online API:
- models **sessions**, not users
- derives presence from **signals over time**
- exposes timestamps and uncertainty
- distinguishes concurrency from totals

When designed this way, presence becomes trustworthy instead of brittle.

---

## Related docs

- [Docs index](./index.md)
- [Online vs Offline Presence](./presence-online-offline.md)
- [Who’s Online API](./whos-online-api.md)
- [Presence with Socket.IO](./presence-socket-io.md)
- [WebRTC Presence](./webrtc-presence.md)

