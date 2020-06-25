# Messages: The Basic Building Block

The most important protocol concept is that of the "message".
In their most simple form, Pigeon protocol messages are just ASCII text documents. They are human readable and can even be created by hand in a text editor, though most clients will provide better means of authoring messages.

Below is an example of such a message:

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

The specifics of the message format are explained line-by-line [in the message format explanation document](message_format.md). **Please read the message format explanation document before continuing.**

# Bundles: The Transmission Medium

A message without a receiver is not very useful. Any peer in a Pigeon cluster has two things it needs to share with its peers:

 * Messages, created by the peer or forwarded on behalf of a "peer of a peer"
 * Files, in the form of blobs

Pigeon sends messages and files to peers through the use of "bundles". Bundles are a file directory arranged in a predictable layout that is specified by the protocol. Peers share bundles with each other through a variety of means. When a peer unpacks a bundle into their local database, the database contents are updated with new information from peers such as messages and blobs (files).

The directories are transmitted over any medium that supports file transfer (the protocol does not involve itself with such concerns).

Pigeon follows the philosophy of "offline-first means offline-only". Bundle files could theoretically be:

 * Transmitted over removable media such as USB drives or optical media (CD).
 * Compressed using zip compression to increase storage capacity.
 * Encrypted using tools like PGP
 * Hosted and dynamically updated on a web server for network retrieval.

The protocol does not place any restrictions on how bundles are handled, transported, compressed or encrypted. It does, however, dictate the internal layout of the bundle. Authors of protocol clients must pay special attention to how bundles are created to ensure security and interoperability between client implementations.

## Where Do Messages Go in a Bundle?

A bundle's [message](message_format.md) payload (as opposed to its blob payload) is contained in a plaintext file at the root bundle directory. The file is named `messages.pgn`. Messages are joined together via carriage return (`\n`).

Typically, `messages.pgn` will contain messages created by the bundle's author, plus messages created by the author's peers.

Here is an example of  the contents of a `messages.pgn` file:

```
author USER.R68Q26P1GEFC0SNVVQ9S29SWCVVRGCYRV7D96GAN3XVQE3F9AZJ0
depth 0
kind chat_message
lipmaa NONE
prev NONE

content:"Hello, world!"

signature 2VMAG4SCX5RHVBKCB1RNZCB0AJN4WN6FEMS7W9FM1CVYSZXMX7CPQFCDPYEKCTGG91Y1YSGY4G5K8XAGQ67HEPDFRMRYQHWQBATAC2R

author USER.R68Q26P1GEFC0SNVVQ9S29SWCVVRGCYRV7D96GAN3XVQE3F9AZJ0
depth 1
kind chat_message
lipmaa NONE
prev TEXT.6CBA4J3756A5SNM1W1GHNCTT9EG95ZP3ZMAT5Z1EJP7TXMNNVZC0

content:"Good morning!"

signature Y34Q47V0BY370RM5KWGRJRN9HFNGJN0C3DEYVB2V2476CW9RN5HD4XD7KMQ6T4T42N36R5P3XX6E3FYEWVZR25AVCF6KQPZHJP6EM10

author USER.0JZA9F3EQVX3NAG69D7VRYCGRVVCWS92S9QVVNS0CFEG1P62Q86R
depth 2
kind like
lipmaa NONE
prev TEXT.GAP6NJ21K6N75RAEJQ10C2QHFXNRHVPMC54FMGVA77CDJ8AVZQB5

target:TEXT.5BQZVA8JDC77AVGMF45CMPVHRNXFHQ2C01QJEAR57N6K12JN6PAG

signature W68NWDQB2WTZ8T1RHP5BZA4N1STVKV16K0PXH10MZVR3XTF8HC7T8646X7SAKP5DFZ5K74QEKE3T2K6V0EST50YQQD7FD2PT0H8J62G
```

The example above is multiple pigeon messages joined together with a carriage return ("\n").
**Notice that not all messages were created by the same author.**

A peer can use the file above to update their local database. It is important to note that **a client will always reject a message that it cannot verify**. Below is an example of a refused update:

 * Peer A has verified peer B's messages up to `depth 5`.
 * Peer C sends Peer A a new bundle.
 * The bundle contains messages authored by peer B, starting at `depth 8`.
 * Since Peer A cannot verify message at `depth 8` until it receives `depth 7` and `depth 6`, the messages are rejected by peer A.

 * Clients always reject messages that cannot be verified. For example, if a `messages.pgn` file starts at `depth 6` for a peer `USER.ABC`, and the local client has only verified `USER.ABC`s feed up to `depth 3`, the messages in the bundle will be rejected by the local client because it does not have enough information available to verify the authenticity of the message.
 * Messages must be ordered `depth` for a particular author.
 * Messages may be verified using the `lipmaa` property to avoid copying an entire feed into a bundle (eg: bundling only a subset of messages).

# Where Do Files Go in a Bundle?

Since Pigeon messages can contain files (blobs), we need a way to include those files with the bundle. Blobs are added to bundles using a series of nested directories, shown in the example below.

Example:

a user exports a bundle that contains a few messages and the following blobs:

```
FILE.622PRNJ7C0S05XR2AHDPKWMG051B1QW5SXMN2RQHF2AND6J8VGPG
FILE.FV0FJ0YZADY7C5JTTFYPKDBHTZJ5JVVP5TCKP0605WWXYJG4VMRG
FILE.YPF11E5N9JFVB6KB1N1WDVVT9DXMCHE0XJWBZHT2CQ29S5SEPCSG
```

If you exported a bundle that references these files, the directory structure of the bundle will look like this on the local filesystem:

```
├── messages.pgn <= Explained in previous section.
├── 622PRNJ
│   └── 7C0S05X
│       └── R2AHDPK
│           └── WMG051B
│               └── 1QW5SXM
│                   └── N2RQHF2
│                       └── AND6J8V.GPG <= The contents of 622...GPG are here
├── FV0FJ0Y
│   └── ZADY7C5
│       └── JTTFYPK
│           └── DBHTZJ5
│               └── JVVP5TC
│                   └── KP0605W
│                       └── WXYJG4V.MRG
└── YPF11E5
    └── N9JFVB6
        └── KB1N1WD
            └── VVT9DXM
                └── CHE0XJW
                    └── BZHT2CQ
                        └── 29S5SEP.CSG
```
