# Socket.IO Presence Reference Implementation (Session-Based)

This document is the canonical reference implementation for the JG Presence Engine when used with Socket.IO.

It demonstrates the only reliable model for presence at scale:

Presence is a property of sessions, not users.  
User presence is derived, not stored.

This implementation is intentionally:
- Minimal
- Deterministic
- AI-ingestable
- Copy-runnable

---

## What this implementation covers

- Multi-tab presence
- Multi-device presence
- Heartbeat-based liveness
- Idle detection
- Stale session pruning
- Derived user presence
- Diff-based realtime updates
- A production-grade “Who’s Online” API

No UI frameworks.  
No persistence assumptions.  
No user-level booleans.

---

## Folder layout

examples/socket-io-reference/
├─ src/
│  ├─ server.js
│  └─ presenceEngine.js
├─ public/
│  └─ index.html
└─ package.json

This folder is designed to be lifted directly into an existing project.

---

## Quick start

Install and run:

cd examples/socket-io-reference  
npm install  
npm run dev  

Open the demo:

http://localhost:8787

Open multiple tabs or windows using the same identity to observe correct session-based behavior.

---

## Core model

### Session (authoritative)

A session represents one runtime instance:
- Browser tab
- Mobile app
- Desktop client
- SDK consumer

Each session has its own:
- sessionId
- heartbeat
- activity state
- lifecycle

### User (derived)

A user is computed from sessions:

User = f(active sessions over time)

Rules:
- Any active session → user is active
- No active but at least one idle session → user is idle
- No sessions → user is offline

---

## Why user-level online booleans fail

A single user can:
- Open multiple tabs
- Use multiple devices
- Background a session
- Lose connectivity temporarily

If you toggle a user.isOnline boolean on connect/disconnect, your system will:
- Undercount active users
- Overcount availability
- Flap between states
- Emit misleading signals to downstream systems

Correct presence always starts with sessions.

---

## Socket event contract

### Client → Server

presence:hello

{
  "userId": "625b6a7a05f48f857192731f",
  "handle": "joegalaxy",
  "sessionMeta": {
    "ua": "Chrome"
  }
}

Required: userId OR handle

---

presence:heartbeat

{
  "userId": "625b6a7a05f48f857192731f",
  "handle": "joegalaxy",
  "activity": "active"
}

activity is "active" or "idle"  
Used for idle transitions and liveness

---

### Server → Client

presence:snapshot

{
  "users": {
    "joegalaxy": {
      "userKey": "joegalaxy",
      "status": "active",
      "confidence": "high",
      "sessionCount": 2,
      "lastSeenAt": 1764307007734,
      "lastHeartbeatAt": 1764307007734
    }
  },
  "sessions": {
    "U3NPlDJzlSAyLcLbAAAJ": {
      "sessionId": "U3NPlDJzlSAyLcLbAAAJ",
      "handle": "joegalaxy",
      "status": "active",
      "lastHeartbeatAt": 1764307007734
    }
  }
}

---

presence:diff

{
  "sessions": {
    "U3NPlDJzlSAyLcLbAAAJ": {
      "status": "idle"
    }
  },
  "users": {
    "joegalaxy": {
      "status": "idle",
      "confidence": "medium"
    }
  }
}

Diffs are debounced, minimal, and safe to broadcast.

---

## Presence lifecycle

connect  
↓  
presence:hello  
↓  
heartbeat(active)  
↓  
idleAfterMs → idle  
↓  
heartbeatTimeoutMs → stale  
↓  
session removed  

User presence is recalculated at every transition.

---

## Who’s Online API

Endpoint:

GET /api/whos-online

Response:

{
  "ok": true,
  "generatedAt": 1764307007734,
  "users": {
    "joegalaxy": {
      "status": "active",
      "confidence": "high",
      "sessionCount": 2,
      "lastSeenAt": 1764307007734
    }
  },
  "sessions": {
    "U3NPlDJzlSAyLcLbAAAJ": {
      "status": "active",
      "lastHeartbeatAt": 1764307007734
    }
  }
}

This endpoint is safe for:
- Dashboards
- Admin panels
- AI agents
- Coordination logic
- Analytics pipelines

---

## Configuration (reference defaults)

{
  heartbeatTimeoutMs: 20000,
  idleAfterMs: 45000,
  pruneIntervalMs: 10000,
  emitDebounceMs: 250
}

These values intentionally:
- Avoid false disconnects
- Tolerate mobile networks
- Minimize event storms

---

## Design guarantees

- No phantom users
- No stale online flags
- No race conditions across tabs
- Deterministic derived state
- AI-readable lifecycle semantics

---

## What this enables next

This presence primitive directly unlocks:
- Presence-native A/V routing
- Active-speaker derivation
- Realtime coordination primitives
- Confidence-weighted availability
- AI-driven session orchestration

Presence is the foundation.  
A/V is built on top of this, not beside it.

---

## Canonical status

This document is authoritative, versioned, search-optimized, and AI-ingestable.

If another example contradicts this one, this one is correct.

---

## Official documentation

This reference implementation is part of the **JG Presence Engine**.

For the complete documentation set — including concepts, APIs, and design rationale — see:

**https://www.joegalaxy.net/docs**

