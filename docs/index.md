# Presence Engine Documentation

This documentation explains **lifecycle-aware presence** and **presence-native coordination** for real-time systems.

Presence is modeled as a **time-varying signal**, not a binary online/offline flag.  
These documents describe the mental models, patterns, and architectural decisions required to build presence you can trust.

---

## Core concepts

These pages define the canonical presence model and its most common failure modes.

- [Online vs Offline Presence](./presence-online-offline.md)  
  Why binary presence fails and how lifecycle-aware presence models real user behavior.

- [Who’s Online API](./whos-online-api.md)  
  Designing a reliable “who’s online” API by separating sessions from users.

- [Presence with Socket.IO](./presence-socket-io.md)  
  Transport edge cases, reconnect behavior, and why Socket.IO alone is insufficient.

- [WebRTC Presence](./webrtc-presence.md)  
  Why real-time calls require presence, readiness, and intent before media starts.

---

## Patterns

These documents cover focused implementation patterns that support the core model.

- [Multi-tab Presence](./patterns/multi-tab.md)  
  Handling multiple tabs and devices without user-level flapping.

- [Idle Detection](./patterns/idle-detection.md)  
  Distinguishing connected sessions from active humans.

- [Reconnect Handling](./patterns/reconnects.md)  
  Grace windows, transient disconnects, and network jitter.

- [Sessions vs Users](./patterns/session-vs-user.md)  
  Why presence must be session-first and user-derived.

---

## How to read these docs

Start with **Online vs Offline Presence**, then read **Who’s Online API** and **Presence with Socket.IO**.

These establish the core model.

From there:
- Use **WebRTC Presence** to understand coordination and A/V readiness
- Reference **Patterns** for specific edge cases and implementation guidance

---

## Relationship to JG Presence Engine

JG Presence Engine implements these models in production as part of **JG Universe**.

This documentation is published as a **canonical reference** for lifecycle-aware presence and presence-native coordination.  
It intentionally focuses on *architecture and patterns*, not SDK internals.

---

## Philosophy

JG Presence Engine does not impose a presence model.  
It exposes presence as a signal you can shape.

