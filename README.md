![](logo.png)

# Pigeon

A synchronizing peer-to-peer messaging protocol that is:

 * decentralized (peer-to-peer)
 * replicated
 * tamper-resistant
 * delay tolerant
 * built for [sneakernet](https://en.wikipedia.org/wiki/Sneakernet) from the ground up

The document below describes a protocol as it _should be_ rather than as it is. This document does not describe a working protocol. It is a planning document for a protocol and the first software packages that will implement the protocol.

# Why?

Pigeon can serve a number of use cases. Below are some examples:

 * Systems with low connectivity or uptime (remote sensor logging, maritime systems, solar systems with intermittent power, IoT systems with poor network connectivity)
 * Store-and-forward message gateways, such as a [data mule](https://en.wikipedia.org/wiki/Data_mule).
 * Censorship resistant communications
 * [Delay tolerant networking](https://en.wikipedia.org/wiki/Delay-tolerant_networking)
 * Applications that require a high level of data-integrity or auditing.
 * delay-tolerant peer-to-peer social networks, games, file sharing etc...
 * Time series data storage

# What Does It Do?

Each node in a swarm of peers has a local "log". The log is an append only feed of messages written in an ASCII-based serialization format. Messages are signed with a secret key to validate a message's integrity and to prevent tampering by untrusty peers. Nodes in the swarm "follow" other logs from peers of interest. Nodes always replicate the logs of their peers and "gossip" information about peers across the swarm. Gossip information is packaged into "bundles" which contain backups of peer logs in an efficient binary format that can be easily transmitted via sneakernet, direct serial connection, or any other high-latency, high throughput medium.

# How It Works

Sneakernet is the main use case for Pigeon messages to be transmitted. Transmission of SD Cards via postal mail offer an excellent medium for transmission of Pigeon messages, although any data transfer medium is theoretically possible.

![](sync.png)

# Prior Art

This is an exploration of ideas set forth by the Secure Scuttlebutt protocol. It is my opinion that SSB is one of the most innovative protocols created in recent years. Without the research and efforts of the SSBC, this project would not be possible, so a big thanks goes out to all the people who make SSB possible.

I've also been inspired by the compactness and minimalism of [SQLite, which should serve as a role model for all of us](https://www.sqlite.org/talks/wroclaw-20090310.pdf).

# Hard Constraints

 * Have [near] zero config. We will allow a limit of 10 configuration options for all eternity. These are simple key/value pairs. No nesting, no namespacing, no dots, no dashes, no nested config names, no arrays, none of that crap. Seriously, I'm watching you.
 * Offer a portable and interchangeable format for application developers to synchronize data between peers.
 * No singletons. No servers, no client/server differentiation.

# Protocol Vs. Implementation Vs. Application

In the specification below, the terms "protocol", "implementation" and "application" will be used frequently.

The **protocol** is a document that specifies a set of guidelines that implementors must follow when authoring a Pigeon Protocol implementation. Analogy: HTTP is a protocol specified by [RFC 2616](https://tools.ietf.org/html/rfc2616).

An **implementation** is a software library (not an application for end users) that implements the Pigeon protocol specification. Example: LibCurl and LibHTTP are HTTP implementations.

An **application** is software that uses a Pigeon Protocol implementation, possibly augmenting the protocol with additional functionality. Example: Netscape Navigator is an HTTP application.

# The Initial Implementation Should...

 * Allow for importing/exporting to SSB via plugins.
 * Assume CPU and RAM are not plentiful.
 * Assume platform has no networking support. No startups, shutdowns, or reboots
 * Assume storage is plentiful when making resource allocation tradeoffs.
 * Be filesystem agnostic. Persistence mechanisms are implementation-specific.
 * Be Secure
 * Easily be ported to new platforms and languages.
 * Enable "Free listening"
 * Have a formal specification (reference implementations are not OK).
 * Have no singletons (no signing authorities, no servers of any kind, even locally)
 * Minimize conceptual overhead (If it's not needed at least 80% of the time, don't add it).
 * Offline-first. Assume the user will never have TCP or UDP access.
 * Prefer a monolithic internal structure. Avoid external dependencies except for limited use cases (Eg: crypto libs). Do not break things into smaller pieces until there are at least three real-world reasons to do so.
 * Use a serialization format that is deterministic and easy to parse on constrained devices.
 * Assume there are no connections or network (file/blob first)
 * Assume CPU resources and memory are limited.
 * Assume block storage is plentiful (storage space measured in GBs)

# What is Possible?

Below are some possible use cases to illustrate real-world applications. Once protocol implementations exist, the ideas below should be possible.

 * Play-by-mail [patchwork clone](https://github.com/ssbc/patchwork)
 * A GUI database browser for developers that wish to use the protocol for log storage or as a time series DB.
 * A messenger app
 * Secure Scuttlebutt import / export / gateway tool.
 * A newsgroup / NNTP analog.
 * A play-by-mail (or bluetooth) file sharing app.
 * A turn-based board game.
 * An IoT data logger
 * Sync a feed over email (via external tool)
 * Sync a feed over bluetooth (via external tool)
 * Sync a feed over actual pigeons, possibly soliciting help from world famous boxer and pigeon racing enthusiast Mike Tyson.

# Unanswered Questions

 * Ephemeral key exchange
 * Merkle tree vs. hash chain
 * Standardized general purpose message schemas (follow, unfollow, redirect, etc.)
# Concepts

 * Base64: The protocol will use Base 64 Encoding with URL and Filename Safe Alphabet as specified in [RFC 4648](https://tools.ietf.org/html/rfc4648). The protocol always uses the standard "=" character for padding. Deviations from this will be explicitly noted.
 * Identity: A base64 `ed25519` public key string starting with `@` and ending with `.ed25519`.
 * Message Signature: An ED25519 signature starting with a `%` and end with `.sha256`. Messages (covered later) are referenced by a signature.
 * String: A 1..62 byte list of ASCII characters, Starting and ending with `"`.
 * Blob: Arbitrary binary data, with a current max size of 1 MB.
 * Blob Hash: A base64'ed SHA256 of a Blob, starting with `&` and ending with `.sha256`.
 * Pair: One `string` and on of the following: `Blob Hash|Signature|Identity|String`, joined with a `:` character between the two. See "key" and "value" below.
 * Header: A reserved pair of information that is required by the protocol for internal reasons. Not to be confused by a `Pair`, which is user definable. The only Headers the protocol currently uses are `author`, `depth`, `kind`, `prev`. Headers do not have "string quotes" around the key, and the value is delimited by a space (` `) character. Example: `sequence 46`.
 * Key: The value to the left of a `:` in a pair. Always a string.
 * Value: The value to the right of a `:` in a pair. It is always one of the following: `Blob Hash, Signature, Identity, String`.
 * Message: A document with an author, prev, depth, kind, an arbitrary set of attribute pairs and a footer.
 * Footer: ??? TODO ????
 * Signature: ??? TODO ???
 * Kind: A string at the top of a message indicating the message's purpose. Example `"private_message"`, `"mention"`, `"share"`. Kinds may be namespaced by applications using the `.` character.
 * Feed: A linked collection of messages.
 * Null Signature: An ASCII `0` character, used to indicate the first message in a feed (discussed later)
 * Bundle: A specially crafted binary archive sent from one peer directly to another peer for the sake of synchronizing and gossiping feeds. Bundles are intricate and require their own document, found [here](bundles.md)

# What a Message Looks Like

```
author @ajgdylxeifojlxpbmen3exlnsbx8buspsjh37b/ipvi=.ed25519
sequence 23
kind "example"
previous %85738f8f9a7f1b04b5329c590ebcb9e425925c6d0984089c43a022de4f19c281.sha256
timestamp 23123123123

"foo":&3f79bb7b435b05321651daefd374cdc681dc06faa65e374e38337b88ca046dea.sha256
"baz":"bar"
"my_friend":@abcdef1234567890.ed25519
"really_cool_message":%85738f8f9a7f1b04b5329c590ebcb9e425925c6d0984089c43a022de4f19c281.sha256
"baz":"whatever"

signature 1b04b5329c1b04b5329c1b04b5329c1b04b5329c.sig.ed25519
```

# How It Works

```bash

pigeon status
# => BLOBS: 10,234
# => PEERS: 26
# => VERSION: 0.0.1
# => FOO: BAR

pigeon identity new
# => @ajgdylxeifojlxpbmen3exlnsbx8buspsjh37b/ipvi=.ed25519

pigeon identity show
# => @ajgdylxeifojlxpbmen3exlnsbx8buspsjh37b/ipvi=.ed25519


pigeon blob set '"Lol, data"'
# => &2e7a0bc31f3c4fe6114051c3a56c8ed8a030b3b394df7d29d37648e9b8cbf54b.sha256

# Or use pipes for big files:
echo "Lol, data" | pigeon blob set
# => &2e7a0bc31f3c4fe6114051c3a56c8ed8a030b3b394df7d29d37648e9b8cbf54b.sha256
cat "pigeon.jpg" | pigeon blob set
# => &2e7a0bc31f3c4fe6114051c3a56c8ed8a030b3b394df7d29d37648e9b8cbf54b.sha256


pigeon blob get &2e7a0bc31f3c4fe6114051c3a56c8ed8a030b3b394df7d29d37648e9b8cbf54b.sha256
# => "Lol, data"

pigeon message new weather_report
# => "Commiting existing message `%jvK...zGM=.sha256`.
# => "Starting new message of kind `weather_report`.

pigeon message current # Show active log entry.
# => author @ajgdylxeifojlxpbmen3exlnsbx8buspsjh37b/ipvi=.ed25519
# => sequence 1
# => kind weather_report
# => previous %jvKh9yoiEJaePzoWCF1nnqpIlPgTk9FHEtqczQbvzGM=.sha256
# => timestamp 23123123123
# =>
# =>

pigeon blob get 2e7a0bc3 | pigeon message append funy_cat_video

pigeon message save
# => author @ajgdylxeifojlxpbmen3exlnsbx8buspsjh37b/ipvi=.ed25519
# => sequence 1
# => kind &82244417f956ac7c599f191593f7e441a4fafa20a4158fd52e154f1dc4c8ed92.sha256
# => previous %jvKh9yoiEJaePzoWCF1nnqpIlPgTk9FHEtqczQbvzGM=.sha256
# => timestamp 23123123123
# =>
# => current_mood:&2e7a0bc31f3c4fe6114051c3a56c8ed8a030b3b394df7d29d37648e9b8cbf54b.sha256
# =>

pigeon message find %g0Fs9yoiEJaePzoWCF1nnqpIlPgTk9FHEtqczQbvzGM=.sha256
# => author @ajgdylxeifojlxpbmen3exlnsbx8buspsjh37b/ipvi=.ed25519
# => sequence 1
# => kind &82244417f956ac7c599f191593f7e441a4fafa20a4158fd52e154f1dc4c8ed92.sha256
# => previous %jvKh9yoiEJaePzoWCF1nnqpIlPgTk9FHEtqczQbvzGM=.sha256
# => timestamp 23123123123
# =>
# => user_profile:&2e7a0bc31f3c4fe6114051c3a56c8ed8a030b3b394df7d29d37648e9b8cbf54b.sha256
# =>

pigeon message find-all --author=@ajgdylxeifojlxpbmen3exlnsbx8buspsjh37b/ipvi=.ed25519 --since=1
# => author @ajgdylxeifojlxpbmen3exlnsbx8buspsjh37b/ipvi=.ed25519
# => sequence 1
# => kind &82244417f956ac7c599f191593f7e441a4fafa20a4158fd52e154f1dc4c8ed92.sha256
# => previous %jvKh9yoiEJaePzoWCF1nnqpIlPgTk9FHEtqczQbvzGM=.sha256
# => timestamp 23123123123
# =>
# => like:%2e7a0bc31f3c4fe6114051c3a56c8ed8a030b3b394df7d29d37648e9b8cbf54b.ed25519
# =>
# => author @ajgdylxeifojlxpbmen3exlnsbx8buspsjh37b/ipvi=.ed25519
# => sequence 2
# => kind &82244417f956ac7c599f191593f7e441a4fafa20a4158fd52e154f1dc4c8ed92.sha256
# => previous %jvKh9yoiEJaePzoWCF1nnqpIlPgTk9FHEtqczQbvzGM=.sha256
# => timestamp 23123123123
# =>
# => favorite_song:&2e7a0bc31f3c4fe6114051c3a56c8ed8a030b3b394df7d29d37648e9b8cbf54b.sha256
# =>

pigeon peer add @m0LEP+0NrGqu1wT8/4a3nOPuRBM+DrMpUahDZ3/cDi8=.ed25519
# =>

pigeon peer remove @78daXMc/BOq5F1RWLMN4zgPVBVLqA4ShkLgE6z9OUGQ=.ed25519
# =>

pigeon peer block @GOl+398b2kWeLi6+DCcU0i3AWD6vWmUtocBVYbpkpNk=.ed25519
# =>

pigeon peer all
# => @c8hovH5OOzNJ1SXUsIN+zI23xMcvGdEbs3ZJgzpthrw=.ed25519
# => @GOl+398b2kWeLi6+DCcU0i3AWD6vWmUtocBVYbpkpNk=.ed25519
# => @m0LEP+0NrGqu1wT8/4a3nOPuRBM+DrMpUahDZ3/cDi8=.ed25519

pigeon bundle create
# => (creates @GOl+398b2kWeLi6+DCcU0i3AWD6vWmUtocBVYbpkpNk=.ed25519.pigeon)

pigeon bundle consume @GOl+398b2kWeLi6+DCcU0i3AWD6vWmUtocBVYbpkpNk=.ed25519.pigeon
# =>

```
