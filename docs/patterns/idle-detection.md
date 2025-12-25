# Idle Detection: Distinguishing Connected from Active

A connected session is not the same as an active human.

Idle detection is a core presence pattern that separates **transport connectivity** from **human engagement**, preventing false activity signals and improving coordination accuracy.

This document explains how to detect idle state correctly without breaking presence stability.

---

## Why idle detection matters

In real systems:

- browsers keep connections open while tabs are inactive
- users leave tabs open for hours or days
- mobile apps remain connected in the background
- network state does not reflect user attention

If presence treats all connected sessions as active, downstream systems will:

- overestimate availability
- initiate interactions at the wrong time
- degrade trust in presence indicators
- create coordination failures

Idle detection solves this by introducing **activity-aware presence**.

---

## Connected does not mean active

A session may be connected while the user is:

- reading another tab
- away from their device
- backgrounded by the operating system
- idle without interaction

Presence must model **activity**, not just connectivity.

Idle detection provides the signal required to make this distinction.

---

## Activity signals

Idle detection relies on **lightweight activity signals**, such as:

- keyboard input
- pointer movement
- touch events
- focus and visibility changes

These signals should be:

- sampled, not streamed
- rate-limited
- privacy-conscious
- scoped to the session

Idle detection does not require capturing user content or intent.

---

## Session-level idle state

Idle state must be tracked at the **session level**, not the user level.

Each session independently transitions through states such as:

- active
- idle
- away

A user with multiple sessions may be active in one session and idle in another.

User-level presence is derived from session state, not overwritten by it.

---

## Time-based thresholds

Idle detection should be time-based, not event-based.

Typical thresholds:

- short inactivity → idle
- longer inactivity → away
- extended inactivity → stale

Exact timings vary by product and context.

The key principle is that **state is derived from elapsed time since last activity**, not from a single event.

---

## Avoiding false idle transitions

Common mistakes include:

- marking idle immediately on blur
- resetting idle on any background heartbeat
- using overly aggressive timeouts
- tying idle state to socket disconnects

A session may briefly lose focus without the user leaving.

Idle detection must be tolerant of transient state changes.

---

## Deriving user presence with idle sessions

When aggregating user presence from sessions:

- active sessions take precedence
- idle sessions should not override active ones
- away sessions indicate reduced availability, not absence

This ensures that idle detection improves accuracy without causing flapping.

---

## Common failure modes

Idle detection fails when systems:

- treat idle as offline
- store idle state at the user level
- emit excessive idle events
- ignore multi-tab behavior
- conflate idle with disconnection

Idle is a **state of reduced engagement**, not absence.

---

## Summary

Idle detection is essential for truthful presence.

A correct idle detection pattern:

- distinguishes activity from connectivity
- operates at the session level
- uses time-based thresholds
- tolerates transient focus changes
- feeds into derived user presence

When implemented correctly, idle detection makes presence predictable, trustworthy, and coordination-ready.

---

## Related docs

- [Docs index](../index.md)
- [Online vs Offline Presence](../presence-online-offline.md)
- [Who’s Online API](../whos-online-api.md)
- [Multi-Tab Presence](./multi-tab.md)
