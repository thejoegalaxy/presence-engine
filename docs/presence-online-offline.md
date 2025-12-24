# Online vs Offline Presence: A Lifecycle-Aware Model

Online and offline look binary, but **real user sessions are noisy**.  
Tabs suspend, mobile radios sleep, and networks partition without clean disconnects.

This document explains why binary presence fails and how a **lifecycle-aware, signal-based model** produces presence you can trust without flapping or false offline states.

---

## Why binary online/offline presence fails

A socket `connect` event does not mean a human is active.  
A `disconnect` event does not mean they are gone.

Real systems experience:

- background tab suspension  
- mobile OS network freezes  
- transient Wi-Fi handoffs  
- reconnects without explicit closes  

If presence is stored as a user-level boolean:

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

## Presence as a signal

Presence should be modeled as a **time-varying signal**, not a stored flag.

A signal-based model:

- decays without refresh  
- expresses confidence instead of certainty  
- separates transport connectivity from human availability  

This allows systems to coordinate behavior without mistaking network state for intent.

**Presence is a signal, not a status.**

---

## Lifecycle-aware presence states

Lifecycle-aware presence uses explicit states that map to system behavior rather than UI labels.

Typical states include:

- **active** — foreground activity or recent interaction  
- **idle** — connected but not interacting  
- **away** — backgrounded or long idle  
- **stale** — heartbeat missed but within a grace window  
- **offline** — expired session or explicit leave  

State is derived from **time since last signal** and client lifecycle events, not written directly on the user.

---

## Session-first architecture

Presence must be **session-first**, not user-first.

Each browser tab, device, or SDK instance maintains its own session with:

- independent heartbeats  
- independent lifecycle  
- independent expiration  

User-level presence is **derived**, not stored.

This avoids:

- overwriting state from multiple devices  
- false offline transitions  
- brittle reconnect logic  

---

## Architecture and timing

A reliable presence system includes:

- server-authoritative time  
- TTL-based session storage  
- grace windows for jitter  
- adaptive heartbeat intervals  

Offline is a **derived state** when TTL expires, not a reaction to a single disconnect event.

Short heartbeats are used for active sessions.  
Longer intervals apply to idle or background states.

---

## Aggregation and display

If a user-level indicator is required, derive it from the most recent session state.

A simple precedence rule works well:

- **active** beats idle  
- **idle** beats away  
- **away** beats stale  
- **stale** beats offline  

Expose time context when possible.

Displaying “active 30s ago” is more truthful than a brittle online badge.

---

## Common pitfalls

- Treating authentication as presence  
- Storing user-level online flags  
- Expiring sessions immediately on disconnect  
- Ignoring background tab suspension  
- Broadcasting every heartbeat instead of reducing to state changes  
- Using slow databases instead of TTL-based stores  

---

## Minimal pattern (conceptual)

```js
// Server-side: record presence signal
onHeartbeat(sessionId, clientState, now) {
  store.set(
    sessionId,
    { state: clientState, lastSeenAt: now },
    TTL_MS
  );
}

// Derived state on read
function deriveState(lastSeenAt, now) {
  const age = now - lastSeenAt;

  if (age < 15_000) return 'active';
  if (age < 60_000) return 'idle';
  if (age < 180_000) return 'away';
  if (age < 240_000) return 'stale';

  return 'offline';
}
```

Thresholds vary by product.  
The pattern does not.

---

## Summary

Binary online/offline presence collapses under real-world conditions.

A lifecycle-aware, session-first, TTL-derived model produces presence that is:

- stable  
- truthful  
- coordination-ready  

This model scales cleanly from simple activity indicators to presence-native A/V and real-time collaboration.

---

## Related docs

- [Docs index](./index.md)
- [Who’s Online API](./whos-online-api.md)
- [Presence with Socket.IO](./presence-socket-io.md)
- [WebRTC Presence](./webrtc-presence.md)
