# It Starts With a Message...

The most important prtocol concept is that of the "message".
In their most simple form, Pigeon protocol messages are just ASCII text documents. They are human readable and can even be created by hand in a text editor, though most clients will provide better means of authoring messages.

Below is an example of such a message:

```
author @MF312A76JV8S1XWCHV1XR6ANRDMPAT2G5K8PZTGKWV354PR82CD0.ed25519
kind weather_report
prev %ZV85NQS8B1BWQN7YAME1GB0G6XS2AVN610RQTME507DN5ASP2S6G.sha256
depth 3
lipmaa 2

temperature:"22.0C"
webcam_photo:&FV0FJ0YZADY7C5JTTFYPKDBHTZJ5JVVP5TCKP0605WWXYJG4VMRG.sha256
weather_reported_by:@0DC253VW8RP4KGTZP8K5G2TAPMDRNA6RX1VHCWX1S8VJ67A213FM.ed25519

signature JSPJJQJRVBVGV52K2058AR2KFQCWSZ8M8W6Q6PB93R2T3SJ031AYX1X74KCW06HHVQ9Y6NDATGE6NH3W59QY35M58YDQC5WEA1ASW08.sig.ed25519
```

Let's explore each line of a message.

### Line 1: `author`
### Line 2: `kind`
### Line 3: `prev`
### Line 4: `depth`
### Line 5: `lipmaa`
### Line 6: Empty carriage return (body start)
### Lines 7: Entry containing a string
### Lines 8: Entry referencing a blob
### Lines 9: Entry referencing a peer's identity
### Lines 10: Empty Carriage Return (footer start)
### Lines 11: Signature Line
### Lines 12: Empty Carriage Return (message end)

# Sharing Messages and Blobs via Bundles

# Glossary of Terms

**This list is out of date.** Numerous changes and problems were addressed in the implementation of a client. We will update this list in May of 2020.

 * Header
 * Blob
 * Crockford Base32
 * NONE
 * String
 * Signature
 * Value
 * Message
 * Blob Hash
 * Key
 * Pair
 * Footer
 * Bundle
 * Kind
 * Message Signature
 * Feed
 * Identity


# Running a CLI Client

Pigeon currently has one CLI available. It is written in Ruby. Documentation can be found [here](https://tildegit.org/PigeonProtocolConsortium/pigeon_ruby)

# Up Next

This concludes the developer documentation. Please email us with questions. To learn more, continue to the [idea bin](IDEAS.md).
