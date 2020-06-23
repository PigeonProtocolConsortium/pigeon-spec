# Exploring the Pigeon Message Format

In the test that follows, we will explore a pigeon message line-by-line.

The example message is shown in its entirety below:

```
author USER.4CZHSZAH8473YPHP1F1DR5ZVCRKEA4Q0BY18NMXYE14NZ0XV2PGG
depth 123
kind weather_report
lipmaa TEXT.7ZKXANAAM31R9AMHMBVGP9Q5BF5HSCP557981VQHBTRYETGTGAK0
prev TEXT.E90DY6RABDQ2CJPVQHYQDYH6N7Q46SZKQ0AQ76J6D684HYBRKE4G

temperature:"22.0C"
webcam_photo:FILE.FV0FJ0YZADY7C5JTTFYPKDBHTZJ5JVVP5TCKP0605WWXYJG4VMRG
weather_reported_by:USER.GGP2VX0ZN41EYXMN81YB0Q4AEKRCVZ5RD1F1PHPY3748HAZSHZC4

signature JSPJJQJRVBVGV52K2058AR2KFQCWSZ8M8W6Q6PB93R2T3SJ031AYX1X74KCW06HHVQ9Y6NDATGE6NH3W59QY35M58YDQC5WEA1ASW08
```

### Pigeon Multihashes and Data Types

Pigeon has 4 data types:

 * Message multihash
 * Blob multihash
 * User multihash
 * String

### Parts of a Message

The three parts of a Pigeon message are:

1. Header: Data that is used by the _protocol_
2. Body: Data that is used by the _user or application_
3. Footer: Cryptographic signature to prevent tampering or forgery.

The parts of a message must follow the order specified.

### Parts of a Header

A header is the first part of a message and contains 5 subsections:

 1. `author`: A
 1. `depth`:
 1. `kind`:
 1. `lipmaa`:
 1. `prev`:

### Line 1: `Author`

EXAMPLE:

```
author @MF312A76JV8S1XWCHV1XR6ANRDMPAT2G5K8PZTGKWV354PR82CD0.ed25519
```

The first line of a Pigeon message header is the `author` entry.

Every Pigeon database has an "identity". An identity is an ED25519 key pair that prevents tampering by parties other than the database owner. An identity is publicly referenced using a "multihash". In the example above, the identity multihash was `@MF312A76JV8S1XWCHV1XR6ANRDMPAT2G5K8PZTGKWV354PR82CD0.ed25519`.

The steps to generate a valid identity are:

1. Perform [Crockford Base32 encoding](https://www.crockford.com/base32.html) on an ED25519 public key.
2. Add an `@` symbol to the beginning of the string from step 1.
3. Add a `.ed25519` string to the end of the string from step 2.

### Line 2: `Kind`

EXAMPLE:

```
kind weather_report
```

The second line of the header is the `kind` entry. This entry is user definable. The `kind` entry is used as a means of signalling intent to applications that will consume the message.

It must meet the following criteria:

 * Must be 1-90 characters in length
 * Cannot contain whitespace or control characters
 * May contain any of the following characters:
    * alphanumeric characters
    * dashes (`-`)
    * underscores (`_`)
    * Symbols used for multihashes, such as `@`, `&` and `%` (covered later).

### Line 3: `Prev`

EXAMPLE:

```
prev %ZV85NQS8B1BWQN7YAME1GB0G6XS2AVN610RQTME507DN5ASP2S6G.sha256
```

A Pigeon message feed is a unidirectional chain of documents where the newest document points back to the document that came before it in the chain ([example diagram](diagram1.png)).

To create this chain, a Pigeon message uses the `prev` field. The `prev` field contains a message multihash. In this case, the multihash is `%ZV85NQS8B1BWQN7YAME1GB0G6XS2AVN610RQTME507DN5ASP2S6G.sha256`.

Messages are content addressed. This is in contrast to protocols such as HTTP which use names to identify resources. Because Pigeon messages are addressed by content rather than by name, changing a message's content, even by just one character, has the effect of completely changing the message's multihash.

**For the first message of a feed, this value is set to `NONE`.**

Message multihashes are calculated as follows:

1. The first character is a `%` symbol, indicating that it is a `message` rather than an `identity`, `blob` or `string`.
2. The next 52 characters are a [Crockford base 32](https://www.crockford.com/base32.html) SHA512 hash of the previous message's content.
3. The message multihash ends in `.sha512`.

### Line 4: `Depth`

EXAMPLE:

```
depth 3
```

Pigeon messages exist in a linear sequence which only moves forward and never "forks".
Every message has a `depth` field to indicate its "place in line".
Because every message has an ever-increasing integer that never duplicates, every message in a Pigeon feed will have a unique hash. This is true even if messages have identical body content.

### Line 5: `Lipmaa`

**THIS FIELD WAS WRITTEN INCORRECTLY. THIS WILL CHANGE SOON. YOU CAN SAFELY MOVE TO THE NEXT SECTION OF THE DOCS**

This concept was borrowed from the [Bamboo protocol](https://github.com/AljoschaMeyer/bamboo#links-and-entry-verification) and [Helger Lipmaa's thesis](https://kodu.ut.ee/~lipmaa/papers/thesis/thesis.pdf).

The `lipmaa` field (often called a "Lipmaa Link") is a special kind of `prev` field that allows partial verification of feeds. This field makes it possible to verify a single message (or subset of messages) without downloading the entire chain of messages.

![](lipmaa.png)

The `lipmaa` field is calculated as follows:

```ruby
def lipmaa(n)
  # The original lipmaa function returns -1 for 0
  # but that does not mesh well with our serialization
  # scheme. Comments welcome on this one.
  return 0 if n < 1 # Prevent -1, division by zero etc..

  m, po3, x = 1, 3, n
  # find k such that (3^k - 1)/2 >= n
  while (m < n)
    po3 *= 3
    m = (po3 - 1) / 2
  end
  po3 /= 3
  # find longest possible back-jump
  if (m != n)
    while x != 0
      m = (po3 - 1) / 2
      po3 /= 3
      x %= m
    end
    if (m != po3)
      po3 = m
    end
  end
  return n - po3
end
```

### Line 6: Body Start (Empty Line)

Once all headers are added, a client must place an empty line (`\n`) after the header.
The empty line signifies the start of the message body.

Some notes about body entries:

 * The body of a message starts and ends with an empty line (`\n`).
 * Every body entry is a key value pair. Keys and values are separated by a `:` character (no spaces).
 * A key must be 1-90 characters in length
 * A key cannot contain whitespace or control characters
 * A key may contain any of the following characters:
    * alphanumeric characters (a-z, A-Z, 0-9)
    * dashes (`-`)
    * underscores (`_`)
    * Symbols used for multihashes, such as `@`, `&` and `%` (covered later).
 * A value may be a:
   * A string (128 characters or less)
   * A multihash referencing an identity (`@`), a message (`%`) or a blob (`&`).

### Lines 7: Entry Containing a String

EXAMPLE:

```
temperature:"22.0C"
```

Body entries are defined by user and contain key/value pairs of application-specific data.
When a key/value pair represents something other than an identity, blob or message ID, a string is used.
Strings can be used for any type of data that does not fit into the other three categories.
Strings must be less than or equal to 128 characters in length.
The example above is the most simple kind of body entry. It specifies an arbitrary string representing the current temperature.

### Lines 8: Entry Referencing a Blob

EXAMPLE:

```
webcam_photo:&FV0FJ0YZADY7C5JTTFYPKDBHTZJ5JVVP5TCKP0605WWXYJG4VMRG.sha256
```

Applications may attach files to messages in the form of blobs. Blobs are referenced using a blob multihash.

 * Starts with a `&` character.
 * Ends with `.sha256`
 * Contains exactly 52 characters between the `&` and `.sha256` parts. This is a SHA256 hash of the blob's content, represented in Crockford Base 32 encoding.

A blob is referenced in a message's key or value. A client will include a blob's content in a "bundle" (explained later).

### Lines 9: Entry Referencing a Peer's Identity

EXAMPLE:

```
weather_reported_by:@0DC253VW8RP4KGTZP8K5G2TAPMDRNA6RX1VHCWX1S8VJ67A213FM.ed25519
```

A message may reference other identities (or its own identity) by using an identity sigil either in the key or value portion of the entry.
This is analogous to "social tagging" seen in many social networks.

### Lines 10: Empty Carriage Return (Footer Start)

The last part of a message is the footer. Like a message body, a message footer starts and ends with an empty line.
The footer is essential for ensuring the tamper resistant properties of a Pigeon message.

### Lines 11: Signature Line

EXAMPLE:

```
signature JSPJJQJRVBVGV52K2058AR2KFQCWSZ8M8W6Q6PB93R2T3SJ031AYX1X74KCW06HHVQ9Y6NDATGE6NH3W59QY35M58YDQC5WEA1ASW08.sig.ed25519
```

A signature starts with the word `signature` followed by a space.
After that, the body (including the trailing `\n`) is signed using the author's ED25519 key.
The signature is encoded with Crockford base 32.
The signature ends with `.sig.ed25519`.
An empty carraige return is added after the signature line.
