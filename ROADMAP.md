
# Roadmap

## Phase I: Build a Working Client

**Completed April 2020**

This is the brainstorming phase where the initial proof-of-concept clients will be written. The first protocol client will be slow and may not be suitable for embedded use within a larger application.

This phase is complete when there is at least one functioning client implementation.

## [Current] Phase II: Build a Working Application

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

After that, I might rename the project so that we are not tied to the legacy baggage of the prototype phase. It might be fun to apply to a grant for continued maintenance (or just lock down the feature set- it's too early to say).


# Up Next

This concludes the roadmap. To learn more, continue to the [Developer Docs and Specification](DEV_DOCS.md).