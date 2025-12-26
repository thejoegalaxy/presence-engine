# JG Presence Engine

**Lifecycle-aware presence for real-time systems.**

JG Presence Engine models presence as a **time-varying signal**, not a binary online/offline flag.  
It is designed for applications where coordination, readiness, and continuity matter more than simple connectivity.

This repository documents the **mental model, patterns, and architecture** behind presence-native systems.  
It is intentionally **not** a full SDK implementation.

---

## Why online/offline presence fails

Most systems treat presence as a boolean:

- connected = online  
- disconnected = offline  

This breaks in real applications.

Browsers suspend background tabs.  
Mobile radios sleep.  
Networks partition and reconnect without clean closes.

A socket connect does not mean a human is active.  
A disconnect does not mean they are gone.

Binary presence produces **false negatives, flapping state, and missed coordination**.

---

## Presence as a signal

Presence should be modeled as a **signal over time**, not a stored property.

A signal model:
- decays without refresh
- expresses confidence instead of certainty
- separates transport connectivity from human availability

This allows downstream systems to make **coordination decisions** without conflating identity, UI state, and network events.

> **Presence is a signal, not a status.**

---

## Lifecycle-aware presence

Lifecycle-aware presence maps system behavior to explicit states:

- **active** — recent interaction or foreground activity  
- **idle** — connected but not interacting  
- **away** — backgrounded or long idle  
- **stale** — heartbeat missed within a grace window  
- **offline** — expired session or explicit leave  

State is derived from **time since last signal** and client lifecycle events — not set directly on the user.

---

## Session-first architecture

Presence must be **session-first**, not user-first.

Each browser tab, device, or SDK instance maintains its own session:
- independent heartbeats
- independent lifecycle
- independent expiration

User-level presence is **derived**, not stored.

This avoids:
- overwriting state from multiple devices  
- false offline transitions  
- brittle reconnect logic  

---

## Presence-native coordination

When presence is modeled correctly, coordination becomes possible.

Systems can:
- avoid blind calls  
- wait for readiness instead of ringing  
- route interaction based on density and intent  
- distinguish availability from mere connectivity  

This is foundational for **presence-native A/V**, real-time collaboration, and multiplayer systems.

---

## Minimal pattern (pseudo-code)

```js
// Server-side heartbeat handling
onHeartbeat(sessionId, clientState, now) {
  store.set(sessionId, {
    state: clientState,
    lastSeenAt: now
  }, TTL_MS);
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

## Common pitfalls

- Treating authentication as presence  
- Storing user-level online flags  
- Expiring sessions immediately on disconnect  
- Ignoring background tab suspension  
- Broadcasting every heartbeat instead of reducing to state changes  
- Using slow databases instead of TTL-based stores  

---

## Documentation

Canonical explanations live here:

- **Online vs Offline Presence** — lifecycle-aware model  
- **Who’s Online API** — sessions vs users  
- **Presence with Socket.IO** — edge cases and transport limits  
- **WebRTC Presence** — why calls need readiness  

See:  
https://www.joegalaxy.net/docs

---

## Relationship to JG Presence SDK

JG Presence Engine is implemented as a production system and SDK used by **JG Universe**.

This repository documents the **patterns and mental models** that inform that implementation.

SDK access is currently limited.

---

## Philosophy

JG Presence Engine does not impose a presence model.  
It exposes presence as a signal you can shape.

---

**Live presence demo:** https://www.joegalaxy.net/presence-demo  
**SDK integration demo:** https://presence-sdk-demo.joegalaxy.net/

---

## License

All rights reserved.

This repository is published for documentation and reference purposes.
