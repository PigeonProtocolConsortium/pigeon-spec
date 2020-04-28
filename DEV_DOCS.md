# Concepts

**This list is out of date.** Numerous changes and problems were addressed in the implementation of a client. We will update this list in May of 2020.

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
 * NONE: ????
 * Bundle: A specially crafted text archive sent from one peer directly to another peer for the sake of synchronizing and gossiping feeds. Bundles are intricate and require their own document, found [here](bundles.md)


# Running a CLI Client

Pigeon currently has one CLI available. It is written in Ruby. Documentation can be found [here](https://tildegit.org/PigeonProtocolConsortium/pigeon_ruby)

# Up Next

This concludes the roadmap. To learn more, continue to the [idea bin](IDEAS.md).