---
layout: default
title: Wire Protocol
permalink: /protocol-reference/wire-protocol
parent: Protocol Reference
---

# {{ page.title }}

What are the actual bytes travelling on the wire? They all seem to be json payload followed by a newline character...

## S->C State stream

- Channel: 0 ("CHANNEL_STATEDIFFS")
- Kind: Unreliable
- Payload specification: [State](state-diffs)

```
<<JSON-payload>>\n
```

## C->S Intent

- Channel: 0 ("CHANNEL_STATEDIFFS")
- Kind: Unreliable
- Payload specification: [Intent](intent)

```
<<JSON-payload>>\n
```

## S->C Interaction

- Channel: 1 ("CHANNEL_COMMANDS")
- Kind: Reliable
- Payload specification: [Entity to entity interaction](/protocol-reference/#entity-to-entity-interaction-requestresponsepubsub)

```
<<JSON-payload>>\n
```

Version 0 implements this packet as:

```
{
  "cmd": "interact",
  "interact": {
    "from_entity": "...",
    "to_entity": "...",
    "cmd": "..."
  }
}\n
```

## C->S Interaction

- Channel: 1 ("CHANNEL_COMMANDS")
- Kind: Reliable
- Payload specification: [Entity to entity interaction](/protocol-reference/#entity-to-entity-interaction-requestresponsepubsub)

```
<<JSON-payload>>\n
```

## C->S->C Media track packet

- Channel: 3 ("CHANNEL_MEDIA")
- Kind: Unreliable
- Related interaction: [allocate_track](/protocol-reference/interactions#entity-wishes-to-transmit-live-media)

```
<<track-id as 32bit big endian integer>><<raw media payload>>\n
```

Yeah that's right. Even audio packets have
that stupid newline at the end.

## C->S->C Clock sync packet

- Channel: 4 ("CHANNEL_CLOCK")
- Kind: Unreliable
- Payload:

```
{
  "client_clock": 123.0,
  "server_clock": 456.0
}\n
```

This packet is sent from the client once a second with only the `client_clock` field set to its local monotonic clock time.
It is immediately bounced back from the server, with its `server_clock` set.

The client can thus calculate its round-trip latency (`now() - response["client_clock"]`), and delta
between its clock and the server clock. Using all this information, it can then extrapolate the world
time and update `place`'s [`clock`](https://github.com/alloverse/docs/blob/master/specifications/components.md#clock)
component.

## Unspecified

- C->S asset request/response
- S->C asset request/response
- S->C asset force delivery
