![](logo.png)

# Pigeon - The Off Grid Peer-to-Peer Protocol™

A synchronizing peer-to-peer messaging protocol that is:

 * decentralized (peer-to-peer)
 * replicated
 * tamper-resistant
 * delay tolerant
 * built for [sneakernet](https://en.wikipedia.org/wiki/Sneakernet) from the ground up

# Protocol Maturity

The document below describes a protocol as it _should be_ rather than as it is. Although the [first implementation of a Pigeon protocol client](https://tildegit.org/PigeonProtocolConsortium/pigeon_ruby) is nearly complete, it is still a work in progress.

This document does not yet describe a working protocol. It is a planning document for a protocol and the first software packages that will implement the protocol.

# Why?

Pigeon can serve a number of use cases. Below are some examples:

 * Systems with low connectivity or uptime such as remote sensor logging, maritime systems, solar systems with intermittent power, IoT systems with poor network connectivity.
 * Store-and-forward message gateways, such as a [data mule](https://en.wikipedia.org/wiki/Data_mule).
 * Censorship resistant applications, such as peer-to-peer messaging and blogging.
 * [Delay tolerant networking](https://en.wikipedia.org/wiki/Delay-tolerant_networking)
 * Applications that require a high level of data-integrity or auditing.
 * Delay-tolerant peer-to-peer social networks, games, file sharing etc...
 * Time series data storage

# What is Possible?

Below are some possible use cases to illustrate real-world applications. Once protocol implementations exist, the ideas below should be possible.

 * Play-by-mail [patchwork clone](https://github.com/ssbc/patchwork)
 * A GUI database browser for developers that wish to use the protocol for log storage or as a time series DB
 * A messenger app
 * Secure Scuttlebutt import / export / gateway tool
 * A newsgroup / NNTP analog
 * A social mapping / point-of-interest sharing site
 * File sharing app that operates over Bluetooth
 * A compatibility gateway between Secure Scuttlebutt and Pigeon messages.
 * A turn-based board game
 * An IoT data logger
 * Sync a feed over email (via external tool)
 * Sync a feed over bluetooth (via external tool)
 * Sync a feed over actual pigeons, possibly soliciting help from world famous boxer and pigeon racing enthusiast Mike Tyson

# How Pigeon Differs from Traditional Sneakernet

[Sneakernet](https://en.wikipedia.org/wiki/Sneakernet) is a protocol used by ancient civilizations to exchange files between computers with limited internet connectivity. Although Pigeon protocol messages can be exchanged over sneakernet, Pigeon is _not_ sneakernet. Sneakernet messages by themselves are not tamper resistant, nor do they provide redundant backup via peers. In contrast, a Pigeon protocol message is redundantly replicated _beyond_ its intended recipient to neighboring peers ("friend of a friend") via gossip and uses cryptography to guarantee that a message's content has not been altered by a third party.

In summary, Pigeon protocol offers benefits above what a traditional sneakernet can provide. A Pigeon protocol message:

 * Is automatiaclly backed up by peers and peers-of-peers (gossip).
 * Cannot be forged by malicious parties.
 * Cannot be altered by anyone except the author.

# How Does It Work?

Each node in a swarm of peers has a local "log". The log is an append only feed of messages written in an ASCII-based serialization format. Messages are signed with a secret key to validate a message's integrity and to prevent tampering by untrusted peers. Nodes in the swarm "follow" other logs from peers of interest. Nodes always replicate the logs of their peers and "gossip" information about peers across the swarm. Gossip information is packaged into "bundles" which contain backups of peer logs in an efficient binary format that can be easily transmitted via sneakernet, direct serial connection, or any high throughput medium, regardless of latency.

Log synchronization via Sneakernet is the main use case for Pigeon messages to be transmitted. Transmission of SD Cards via postal mail offer an excellent medium for transmission of Pigeon messages, although any data transfer medium is theoretically possible.

![](sync.png)

# What a Message Looks Like

Example 1:

```
author @DYdgK1KUInVtG3lS45hA1HZ-jTuvfLKsxDpXPFCve04=.ed25519
kind hello_world
prev NONE
depth 0

key1:"my_value\n"
key2:"my_value2"
key3:"my_value3"
key4:%jvKh9yoiEJaePzoWCF1nnqpIlPgTk9FHEtqczQbvzGM=.sha256
key5:&29f3933302c49c60841d7620886ce54afc68630242aee6ff683926d2465e6ca3.sha256
key6:@galdahnB3L2DE2cTU0Me54IpIUKVEgKmBwvZVtWJccg=.ed25519

signature DN7yPTE-m433ND3jBL4oM23XGxBKafjq0Dp9ArBQa_TIGU7DmCxTumieuPBN-NKxlx_0N7-c5zjLb5XXVHYPCQ==.sig.ed25519

```

Example 2:

```
author @DYdgK1KUInVtG3lS45hA1HZ-jTuvfLKsxDpXPFCve04=.ed25519
kind second_example
prev %ZTBmYWZlMGU0Nzg0ZWZlYjA5NjA0MzdlZWVlNTBiMmY4ODEyZWI1NTZkODcwN2FlMDQxYThmMDExNTNhM2E4NQ==.sha256
depth 1

hello:"world"

signature AerpDKbKRrcaM9wihwFsPC4YRAfYWie5XFEKAdnxQom7MTvsXd9W39AvHfljJnEePZpsQVdfq2TtBPoQHc-MCw==.sig.ed25519

```

![A hierarchy diagram showing how the message in example 2 points back to example 1, and how example 1 points back to NONE](diagram1.png)

# I Have Internet Access. Why Should I Care?

 * [How Iran Turned Off the Internet](https://thewire.in/tech/how-iran-turned-off-the-internet)
 * [Building Internet-connected things seems obvious today, but what about when there’s no Internet?](https://back7.co/home/raspberry-pi-recovery-kit)
 * [‘Nobody’s got to use the Internet’: A GOP lawmaker’s response to concerns about Web privacy](https://www.washingtonpost.com/news/powerpost/wp/2017/04/15/nobodys-got-to-use-the-internet-a-gop-lawmakers-response-to-concerns-about-web-privacy/)
 * [The death of America's net neutrality and how it affects you](https://www.dw.com/en/the-death-of-americas-net-neutrality-and-how-it-affects-you/a-43934099)
 * [YouTube and Facebook Are Removing Evidence of Atrocities, Jeopardizing Cases Against War Criminals](https://theintercept.com/2017/11/02/war-crimes-youtube-facebook-syria-rohingya/)
 * [Encryption is Not Preventing Law Enforcement from Investigating Crime](https://www.alec.org/article/encryption-is-not-preventing-law-enforcement-from-investigating-crime/)
 * [Iraq introduces nightly internet curfew](https://netblocks.org/reports/iraq-introduces-nightly-internet-curfew-JAp1DKBd)
 * [Building a Low-Tech Internet](https://www.lowtechmagazine.com/2015/10/how-to-build-a-low-tech-internet.html)
 * [Inside Cuba's massive, weekly, human-curated sneakernet](https://boingboing.net/2018/05/03/inside-cubas-massive-weekly.html)
 * [CollapseOS](https://collapseos.org/)
 * [Russian Law Takes Effect that Gives Government Sweeping Power Over Internet](https://www.npr.org/2019/11/01/775366588/russian-law-takes-effect-that-gives-government-sweeping-power-over-internet)
 * [Indian Internet shut down as protests rage against citizenship bill](https://edition.cnn.com/2019/12/12/asia/india-shutdown-citizenship-bill-intl-hnk/index.html)
 * [Google goes offline after fibre cables cut](https://www.bbc.com/news/technology-50851420)
 * [Authoritarian Nations Are Turning the Internet Into a Weapon](https://onezero.medium.com/authoritarian-nations-are-turning-the-internet-into-a-weapon-10119d4e9992)

# Prior Art

Pigeon borrows many of the ideas set forth by the [Secure Scuttlebutt protocol](https://ssbc.github.io/scuttlebutt-protocol-guide/). It is my opinion that SSB is one of the most innovative protocols created in recent years. Without the research and efforts of the [Secure Scuttlebutt Consortium](https://github.com/ssbc), this project would not be possible, so a big thanks goes out to all the people who make SSB possible.

I've also been inspired by the compactness and minimalism of [SQLite, which should serve as a role model for all of us](https://www.sqlite.org/talks/wroclaw-20090310.pdf).

In many ways, this protocol can be considered an amalgam of the best ideas from both SQLite and Secure Scuttlebutt.

# Constraints and Design Philosophy

 * Configuration is bad and should be considered a design comprise in nearly all situations. We will allow a limit of 10 configuration options for all eternity. These are simple key/value pairs. No nesting, no namespacing, no dots, no dashes, no nested config names, no arrays, none of that crap. Seriously, I'm watching you.
 * No singletons. No signing authorities, no servers of any kind, even locally, no differentiation between peers (eg: no "super peers").
 * Support Offline-first by being offline-only. Never incorporate TCP or UDP features ever. Such concerns must be handled by higher-level protocols or by application developers. This is to ensure that the protocol is always a viable option for off-grid use cases.

# Roadmap

## Phase I (You Are Here): Build a Working Client

This is the brainstorming phase where the initial proof-of-concept clients will be written. The first protocol client will be slow and may not be suitable for embedded use within a larger application.

This phase is complete when there is at least one functioning client implementation.

## Phase II: Build a Working Application

Using the protocol client from phase I, build an application which uses Pigeon for simulated real-world conditions. This phase will allow for discovery of problems with the draft specification and the first client implementations.

Please see the "What's Possible" section for a list of applications that may be published.

This phase is complete when there are at least two applications (rather than libs or clients) that utilize the protocol.

## Phase III: Client Improvements

Once a gauntlet of applications have been built and outstanding design problems have been addressed, re-write existing client libraries. Unlike the client built in Phase I, the clients built in this phase will have a focus on:

 * Production-scale performance
 * Stability
 * Portability to targets like WASM, embedded systems, Windows, etc..
 * Ability to be embedded into existing applications.

This phase is complete when:
 * There is a client library that is written in an embeddable language (C, Rust, etc..).
 * There is a client library that can performantly serve a mesh of more than 15 peers in a real-world application.

Nice-to-haves for this phase: see the implementation of a WASM and bare metal (embedded) client.

## Phase IV: Finalize v1 Spec

Once a production-grade client exists, the focus will then become documentation. Using the knowledge gained from phases I-III, we will re-write all documentation, possibly using Gitbook or similar services.

Version 1 of the protocol will be considered complete at this phase and the protocol will be considered "ready for production use".

This phase is complete when the "Pigeon Protocol Handbook" is authored. The handbook will be a guide less than 100 pages long, that can be read by a developer from start to finish (as opposed to being referenced) to help them start writing Pigeon applications.

## Phase V: Stabilize, Maintain, Proliferate

With a finalized spec and a portable client library, the next goal is to promote the product to as many developers as possible and continue to author software that is well suited to the protocol.

This phase will be considered complete when there are three production-scale apps using the libraries authored. By this point, we've hopefully made a difference and helped people regain control of their data and find a new alternative to the current status-quo of "online only" computer applications.

After that, I might rename the project so that we arre not tied to the legacy baggage of the prototype phase. It might be fun to apply to a grant for continued maintanence (or just lock down the feature set- it's too early to say).

# Unanswered Questions

 * Ephemeral key exchange
 * Standardized general purpose message schemas (follow, unfollow, same_as, etc.)

# The Initial Implementation Should...

 * Prefer a monolithic internal structure. Avoid external dependencies except for limited use cases (Eg: crypto libs). Do not break things into smaller pieces until there are at least three real-world reasons to do so. Decoupling a library into a package for only 2 use cases is not acceptable.
 * Assume CPU and RAM are not plentiful.
 * Assume platform has no networking support. No servers. No hooks for startups, shutdowns, or reboots.
 * Assume CPU resources and memory are limited.
 * Assume block storage is plentiful when making resource allocation tradeoffs.
 * Files are better than sessions.
 * ...but be filesystem agnostic. Persistence mechanisms are implementation-specific.
 * Provide tamper resistance. Privacy features will be added in v2.
 * Be easily ported to new platforms and languages.
 * Enable "Free listening"
 * Have a formal specification (reference implementations are not OK).
 * Minimize conceptual overhead (If it's not needed at least 80% of the time, don't add it).
 * Use a serialization format that is deterministic and easy to parse on constrained devices.

# Concepts

**This list is out of date.** Numerous changes and problems were addressed in the implementation of a client. We will update this list when the v0 client is released.

 * Base64: The protocol will use Base 64 Encoding with URL and Filename Safe Alphabet as specified in [RFC 4648](https://tools.ietf.org/html/rfc4648). The protocol always uses the standard "=" character for padding. Deviations from this will be explicitly noted.
 * Identity: A base64 `ed25519` public key string starting with `@` and ending with `.ed25519`.
 * Message Signature: An ED25519 signature starting with a `%` and end with `.sha256`. Messages (covered later) are referenced by a signature.
 * String: A 1..62 byte list of ASCII characters, Starting and ending with `"`.
 * Blob: Arbitrary binary data, with a current max size of 1.4 MB.
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
 * Bundle: A specially crafted text archive sent from one peer directly to another peer for the sake of synchronizing and gossiping feeds. Bundles are intricate and require their own document, found [here](bundles.md)


# Running a CLI Client

**NOTE:** Some of the output examples may have changes. This section will be updated upon completion of the [first implementation of a Pigeon protocol client](https://tildegit.org/PigeonProtocolConsortium/pigeon_ruby).

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
