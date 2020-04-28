![](logo.png)

## Pigeon - The Off Grid Peer-to-Peer Protocol

A synchronizing peer-to-peer messaging protocol that is:

 * decentralized (peer-to-peer)
 * replicated
 * tamper-resistant
 * delay tolerant
 * built for [sneakernet](https://en.wikipedia.org/wiki/Sneakernet) from the ground up

Email `contact` at `vaporsoft.xyz` to ask questions or get involved. Your feedback is solicited and appreciated. I will reply to all messages sent.

## Contents

 * Overview (You are Here)
 * [Frequently Asked Questions](FAQ.md)
 * [Roadmap](ROADMAP.md)
 * [Developer Docs and Specification](DEV_DOCS.md)
 * [Ideas and Features](IDEAS.md)

# Overview

Pigeon can serve a number of use cases. Below are some examples:

 * Systems with low connectivity or uptime such as remote sensor logging, maritime systems, solar systems with intermittent power, IoT systems with poor network connectivity.
 * Store-and-forward message gateways, such as a [data mule](https://en.wikipedia.org/wiki/Data_mule).
 * Censorship resistant applications, such as peer-to-peer messaging and blogging.
 * [Delay tolerant networking](https://en.wikipedia.org/wiki/Delay-tolerant_networking)
 * Applications that require a high level of data-integrity or auditing.
 * Delay-tolerant peer-to-peer social networks, games, file sharing etc...
 * Time series data storage

Framed in a more applied sense, Pigeon could theoretically support applications and use cases such as:

 * A messenger app
 * An IoT data logger
 * A newsgroup / NNTP analog
 * A turn-based board game
 * A microblogging social network
 * Secure Scuttlebutt import / export / gateway tool
 * A social mapping / point-of-interest sharing site
 * Bluetooth file sharing app
 * A GUI database browser for developers that wish to use the protocol for log storage or as a time series DB
 * Sync files over actual pigeons, possibly soliciting help from world famous boxer and pigeon racing enthusiast Mike Tyson

## Prior Art

Pigeon borrows many of the ideas set forth by the [Secure Scuttlebutt protocol](https://ssbc.github.io/scuttlebutt-protocol-guide/). It is my opinion that SSB is one of the most innovative protocols created in recent years. Without the research and efforts of the [Secure Scuttlebutt Consortium](https://github.com/ssbc), this project would not be possible, so a big thanks goes out to all the people who make SSB possible.

I've also been inspired by the compactness and minimalism of [SQLite, which should serve as a role model for all of us](https://www.sqlite.org/talks/wroclaw-20090310.pdf).

In many ways, this protocol can be considered an amalgam of the best ideas from both SQLite and Secure Scuttlebutt.

Pigeon also borrows the [Lipmaa link concept seen in the Bamboo protocol](https://github.com/AljoschaMeyer/bamboo) to allow for partial verification of message feeds.

## How Pigeon Differs from Traditional Sneakernet

[Sneakernet](https://en.wikipedia.org/wiki/Sneakernet) is a protocol used by ancient civilizations to exchange files between computers with limited internet connectivity. Although Pigeon protocol messages can be exchanged over sneakernet, Pigeon is _not_ sneakernet. Sneakernet messages by themselves are not tamper resistant, nor do they provide redundant backup via peers. In contrast, a Pigeon protocol message is redundantly replicated _beyond_ its intended recipient to neighboring peers ("friend of a friend") via gossip and uses cryptography to guarantee that a message's content has not been altered by a third party.

In summary, Pigeon protocol offers benefits above what a traditional sneakernet can provide. A Pigeon protocol message:

 * Is automatically backed up by peers and peers-of-peers (gossip).
 * Cannot be forged by malicious parties.
 * Cannot be altered by anyone except the author.

## How Pigeon Differs From Secure Scuttlebutt (SSB)

As mentioned, Pigeon was inspired mostly by the work of of Secure Scuttlebutt. Pigeon takes a different approach in a few areas, however.

1. No reliance on networking in the core library. Although SSB is theoretically able to support Sneakernet-only operation, it is difficult in practice due to reliance on UDP, TCP, and in the case of pubs, DNS.
1. Pigeon uses a custom key/value serialization format instead of JSON. This has two benefits:
    * Serialization and signing is much simpler. Indentation and whitespace are less likely to cause verification problems.
    * Unlike JSON, pigeon messages do not allow nesting, which promotes simplified message schemas.
1. Pigeon uses Crockford flavored Base32 rather than URL safe Base64. This makes it easier to support old or low powered systems. easier to support FAT16 / embedded systems you might want to have an FAQ section with pretty much this
1. Pigeon was designed for portability from the beginning. It has a small enough conceptual overhead that it will actually be possible to support platforms other than NodeJS. Complicated features (like network support) are ignored in favor of an easy-to-implement standard
1. It uses Lipmaa links, so you can verify a feed without downloading all 10,000 messages. This was inspired by the work of the Bamboo protocol.


## How Does It Work?

Each node in a swarm of peers has a local "log". The log is an append only feed of messages written in an ASCII-based serialization format. Messages are signed with a secret key to validate a message's integrity and to prevent tampering by untrusted peers. Nodes in the swarm "follow" other logs from peers of interest. Nodes always replicate the logs of their peers and "gossip" information about peers across the swarm. Gossip information is packaged into "bundles" which contain backups of peer logs in an efficient binary format that can be easily transmitted via sneakernet, direct serial connection, or any high throughput medium, regardless of latency.

Log synchronization via Sneakernet is the main use case for Pigeon messages to be transmitted. SD Cards sent via postal mail offer an excellent medium for transmission of Pigeon messages, although any data transfer medium is theoretically possible.

![](sync.png)

## What Messages Look Like

Messages use a custom ASCII-based encoding scheme (shown below). Although they are mostly human readable, they are intended to be parsed by third party applications rather than written by hand.

Example 1:

```
author @78V80T9Q7862GW5KTNGSDBKMSA53WE98G8TGFDS6HC9HEABFD64G.ed25519
kind ba16b9f6-a0bc-44e4-b873-da52308186e8
prev %KDKK65CX8GMV7EFCJ4K3J77T38SNDE6DX1TE7AKKGW7X74Z63WKG.sha256
depth 0
lipmaa 0

hello:"World"
this_is_a:"Key"

signature JSSFBAZ58Y73NPWZ912KYS0EZ1WA3V3FAG9VW2TF99B3Z0RH06Q52DS2AEBRPEZJFZWBA1Q4WYR7N19VVGRZ9KDFYAX64PCTA9BEW3G.sig.ed25519

```

Example 2:

```
author @78V80T9Q7862GW5KTNGSDBKMSA53WE98G8TGFDS6HC9HEABFD64G.ed25519
kind b049f082-861f-43f0-bc10-ca97b2b91b2e
prev NONE
depth 1
lipmaa 0

cool_message:%KDKK65CX8GMV7EFCJ4K3J77T38SNDE6DX1TE7AKKGW7X74Z63WKG.sha256

signature X4KF6YM3YMR457VTJ7HGY92F6W65YQBEG3WS5QDFNSAF45KHMDZZZRWK710F04Y6TPM2AJ3W135RSF42V8DAE7MJSSTCHYP7JQG7E10.sig.ed25519
```

![A hierarchy diagram showing how the message in example 2 points back to example 1, and how example 1 points back to NONE](diagram1.png)

## Protocol Maturity

The [first working implementation of a Pigeon protocol client](https://tildegit.org/PigeonProtocolConsortium/pigeon_ruby) is complete. We are temporarily halting feature development to focus on documentation, bug fixes and outreach. Contact us to get involved.

## Constraints and Design Philosophy

 * Support Offline-first by being offline-only. Never incorporate TCP or UDP features ever. Such concerns must be handled by higher-level protocols or by application developers. This is to ensure that the protocol is always a viable option for off-grid use cases.
 * Prefer a monolithic internal structure. Avoid external dependencies except for limited use cases (Eg: crypto libs). Do not break things into smaller pieces until there are at least three real-world reasons to do so. Decoupling a library into a package for only 2 use cases is not acceptable.
 * Maintain ecosystem diversity by having a protocol that can be easily and entirely ported to new languages and platforms.
 * No singletons. No signing authorities, no servers of any kind, even locally, no differentiation between peers (eg: no "super peers").
 * Configuration is always a design comprise. We will allow a limit of 10 configuration options for all eternity. These are simple key/value pairs. No nesting, no namespacing, no dots, no dashes, no nested config names, no arrays, none of that crap. Seriously, I'm watching you.
 * Assume CPU and RAM are not plentiful.
 * Assume platform has no networking support. No servers. No hooks for startups, shutdowns, or reboots.
 * Assume block storage is plentiful when making resource allocation tradeoffs.
 * Files are better than sessions, but be filesystem agnostic. Persistence mechanisms are implementation-specific.
 * Provide tamper resistance. Privacy features will be added later.
 * Enable "Free listening"
 * Have a formal specification (reference implementations are not OK).
 * Minimize conceptual overhead (If it's not needed at least 80% of the time, don't add it).
 * Use a serialization format that is deterministic and easy to parse on constrained devices.
 * Natural is better than simple. Convention over configuration. Do not make plugins for common use cases unless it would hurt portability.
 * Backwards compatibility. Numerous compromises have been made to support legacy systems, such as devices that lack network support and FAT16 file systems.

## Non-Goals

 * Extreme configurability.
 * Networking support at the protocol level (TCP, UDP, SSH, HTTP, Etc..)

# Up Next

This concludes the overview. To learn more, continue to [frequently asked questions](FAQ.md)