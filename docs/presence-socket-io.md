# Presence with Socket.IO: Edge Cases and a Reliable Model

Socket.IO makes real-time transport easy, but **transport is not presence**.  
A connected socket does not guarantee a human is active, and a disconnect does not guarantee they are gone.

This guide explains the most common failure modes of Socket.IO presence and a lifecycle-aware approach that avoids flapping states, false offline, and multi-tab conflicts.

---

## Why “socket connected = online” fails

A Socket.IO `connect` event only means:
- a client successfully established a transport channel

It does **not** mean:
- the user is actively using the app
- the tab is in the foreground
- the network is stable
- the session will remain alive

Browsers suspend background tabs.  
Mobile operating systems suspend network stacks.  
Wi-Fi handoffs can drop packets without clean disconnects.

If your presence model is:

- `connect` → online  
- `disconnect` → offline  

your UI will flap between states and users will interpret it as instability.

---

## Presence should be derived, not toggled

A robust presence system is derived from **signals over time**, not from single socket events.

The server should treat presence as:
- a **lastSeenAt** timestamp per session
- **TTL-based expiration** for offline
- **grace windows** for network jitter
- **derived lifecycle states**, not stored flags

Socket.IO is only the transport that carries these signals.

---

## Session-first: the multi-tab problem

Socket.IO makes multi-tab presence tricky:

- one user can have multiple tabs open
- each tab maintains its own socket connection
- one disconnect must not mark the entire user offline

The correct model:
- track **sessions**, not users
- derive user-level presence from the most recent session state

A simple aggregation rule:

- **active** beats idle  
- **idle** beats away  
- **away** beats stale  
- **stale** beats offline  

This avoids user-level flapping caused by individual tabs.

---

## Edge cases you must handle

### 1. Disconnect storms and transport fallbacks

Socket.IO may reconnect aggressively or switch transports.  
Temporary disconnects are common.

**Do not treat a disconnect as offline immediately.**  
Use TTL expiration with a grace window.

---

### 2. Background tabs

The socket may still be connected while the human is gone.

Presence must incorporate:
- document visibility (`visible` vs `hidden`)
- recent interaction timestamps
- longer heartbeat intervals for backgrounded tabs

---

### 3. Mobile suspension

Mobile operating systems can freeze networking without a clean close.

Server-side TTL expiration is required.  
Disconnect events are not reliable.

---

### 4. Network partitions

Packets may drop for minutes without a close frame.

If you mark offline instantly, presence will oscillate between states.

---

### 5. Reconnect identity

Reconnects may create a new `socket.id`.

Presence must bind to a **stable session identifier** you control — not only `socket.id`.

---

## A reliable pattern (minimal)

### Client: heartbeat + lifecycle signals

Clients should emit lightweight heartbeats that include lifecycle state:

- active (foreground interaction)
- idle (no recent interaction)
- background (hidden or suspended)

---

### Server: record signal and derive state

```js
// Record heartbeat signal
onHeartbeat(sessionId, clientState, now) {
  store.set(
    sessionId,
    { state: clientState, lastSeenAt: now },
    TTL_MS
  );
}

// Derive lifecycle state at read time
function deriveState(lastSeenAt, now) {
  const age = now - lastSeenAt;

  if (age < 15_000) return 'active';
  if (age < 60_000) return 'idle';
  if (age < 180_000) return 'away';
  if (age < 240_000) return 'stale';

  return 'offline';
}
```
---

**Key principle**

> **Offline is derived from time, not a disconnect event.**

---

## Reduce noise: publish state changes, not heartbeats

A common mistake is broadcasting every heartbeat to all clients.

Instead:

- store heartbeats quietly  
- compute derived state  
- broadcast only on **meaningful state transitions**

This preserves real-time responsiveness without flooding the network.

---

## Summary

Socket.IO is a transport.  
Presence is a model.

If you:

- treat connect/disconnect as truth  
- store user-level online flags  
- ignore lifecycle and TTL  

presence will be noisy and unreliable.

A session-first, lifecycle-aware, TTL-derived model produces presence you can trust.

---

## Related docs

- Docs: https://www.joegalaxy.net/docs  
- **Online vs Offline Presence** — lifecycle-aware model  
- **Who’s Online API** — sessions vs users  
- **WebRTC Presence** — readiness before calls
