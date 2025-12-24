# WebRTC Presence: Readiness Before Calls

WebRTC enables real-time audio and video, but **media alone does not create successful interaction**.  
Most A/V systems fail because calls are initiated without context, readiness, or intent.

This document explains why **presence must exist before WebRTC**, and how lifecycle-aware presence turns blind calls into coordinated interactions.

---

## The problem with blind calls

Traditional WebRTC flows look like this:

1. User clicks “Call”
2. Media negotiation starts
3. The other side may or may not answer

This model assumes:
- the recipient is online
- the recipient is available
- the timing is appropriate

In real systems, these assumptions are often false.

The result:
- missed calls
- repeated retries
- notification spam
- degraded trust

Media quality does not fix coordination failures.

---

## Presence before media

Presence answers questions WebRTC cannot:

- Is anyone here?
- Are they active or idle?
- Are they available to talk?
- Is the room warming up or empty?

When presence exists **before** media:
- calls stop being blind
- interactions start when readiness exists
- media becomes an outcome, not a gamble

Presence is not an enhancement to WebRTC.  
It is a prerequisite.

---

## Readiness vs availability

Availability is binary and misleading.  
Readiness is contextual and time-based.

A presence-native system can express readiness as:
- active presence
- recent interaction
- declared intent
- room density
- lifecycle state

Calls should start when readiness crosses a threshold, not when a button is clicked.

---

## Rooms that exist before calls

In a presence-native model, rooms are **persistent coordination spaces**, not ephemeral calls.

A room can exist:
- before audio/video starts
- while participants gather
- after media ends

Presence within a room shows:
- who is present
- who is arriving
- who is idle
- momentum over time

Media attaches only when it adds value.

---

## Intent-aware call initiation

If a target is offline or idle, calling immediately is often wrong.

Instead of ringing, a system can:
- record intent to connect
- notify the target asynchronously
- allow them to join when ready

This transforms calling into **coordination**, not interruption.

Presence makes intent observable.

---

## Guest participation

Presence-first WebRTC enables guest access without commitment.

Guests can:
- observe room presence
- join silently
- become active later
- authenticate only when needed

This lowers friction and improves adoption, especially for:
- communities
- events
- live collaboration
- social platforms

Identity layers in after presence.

---

## Observability and coordination intelligence

When presence and WebRTC are combined correctly, systems gain insight beyond media metrics.

Examples:
- room readiness curves
- join and drop-off patterns
- attention decay
- density thresholds for starting or ending interaction

This is not call analytics.  
It is **coordination intelligence**.

---

## A minimal conceptual flow

```text
presence established
   ↓
readiness observed
   ↓
intent signaled (if needed)
   ↓
media attached
   ↓
interaction
   ↓
presence persists after media
```

This flow avoids blind calls and reduces coordination friction.

---

## Summary

WebRTC solves media transport.  
Presence solves coordination.

When combined:

- calls start at the right time  
- rooms feel alive instead of empty  
- users trust the system  
- interaction becomes predictable  

**Presence-native WebRTC is not about better calls.**  
It is about better timing.

---

## Related docs

- [Docs index](./index.md)
- [Online vs Offline Presence](./presence-online-offline.md)
- [Who’s Online API](./whos-online-api.md)
- [Presence with Socket.IO](./presence-socket-io.md)
