# Reconnect Handling: Graceful Recovery Without Flapping

Network disconnects are normal.

Browsers sleep, mobile radios pause, Wi-Fi handoffs occur, and connections drop without warning.  
A correct presence system treats reconnects as **expected behavior**, not failure.

This document explains how to handle reconnects gracefully without causing user-level flapping or false offline states.

---

## Why reconnects are not failures

In real-world environments:

- laptops change networks
- mobile devices suspend radios
- background tabs pause timers
- brief packet loss interrupts connections

A disconnect event does not mean a user has left.  
A reconnect event does not mean a new session has begun.

Presence systems must model reconnects as **continuations**, not exits and re-entries.

---

## Disconnect does not equal offline

A common mistake is treating any disconnect as an immediate offline signal.

This causes:

- false offline transitions
- rapid online/offline flapping
- unnecessary notifications
- loss of trust in presence indicators

Offline should be a **derived state**, reached only after a grace period expires.

---

## Grace windows and tolerance

Reconnect handling requires **grace windows**.

A grace window is a short period during which a session is allowed to reconnect without changing its derived presence state.

During the grace window:

- presence remains unchanged
- the session is marked as potentially reconnecting
- no offline events are emitted

If the session fails to reconnect before the window expires, presence transitions normally.

---

## Session identity and continuity

Reconnects should preserve **session identity** whenever possible.

A stable session identifier allows:

- reconnects to resume the same session
- lifecycle state to continue smoothly
- idle and activity timers to remain meaningful

Relying solely on transport-level identifiers (such as socket IDs) breaks continuity.

Session identity must be owned by the application, not the transport.

---

## Reconnect versus new session

Not every reconnect should be treated as a continuation.

A reconnect may be treated as a new session when:

- the grace window has expired
- the client explicitly resets state
- the reconnect occurs after a long absence

The distinction is temporal, not event-based.

Time determines continuity.

---

## Avoiding flapping during reconnect storms

Rapid connect/disconnect cycles can occur during:

- unstable networks
- VPN changes
- device sleep/wake transitions

To prevent flapping:

- suppress state transitions during the grace window
- debounce reconnect events
- recompute presence from timestamps, not events

Presence should reflect **confidence**, not instantaneous events.

---

## Interaction with idle detection

Reconnect handling must integrate with idle detection.

A reconnect does not automatically imply activity.

After reconnect:

- restore prior lifecycle state
- wait for activity signals
- avoid forcing active state prematurely

This preserves presence accuracy across reconnects.

---

## Common failure modes

Reconnect handling fails when systems:

- mark offline on every disconnect
- treat every reconnect as a new user
- reset idle timers on reconnect
- emit excessive reconnect events
- ignore session identity

These failures stem from **event-first modeling**.

---

## Summary

Reconnects are normal.

A correct reconnect handling pattern:

- uses grace windows
- preserves session identity
- derives presence from time, not events
- suppresses flapping
- integrates with idle detection

When reconnects are handled correctly, presence remains stable under real-world network conditions.

---

## Related docs

- [Docs index](../index.md)
- [Online vs Offline Presence](../presence-online-offline.md)
- [Who’s Online API](../whos-online-api.md)
- [Presence with Socket.IO](../presence-socket-io.md)
- [Idle Detection](./idle-detection.md)
