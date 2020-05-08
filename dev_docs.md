# Messages: The Basic Building Block

The most important protocol concept is that of the "message".
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

The specifics of the message format are explained line-by-line [in the message format explanation document](message_format.md).
Please read this document before continuing.

# Bundles: The Transmission Medium

A message without a receiver is not very useful. Any peer in a Pigeon cluster has two things it needs to share with its peers:

 * Messages, created by the peer or forwarded on behalf of a "peer of a peer"
 * Files, in the form of blobs

Pigeon sends messages and files to peers through the use of "bundles", which are simply a filesystem directories that is arranged in a predictable layout. The directories are transmitted over any medium that supports file transfer (the protocol does not involve itself with such concerns).

Pigeon follows the philosophy of "offline-first means offline-only". Bundle files could theoretically be:

 * Transmitted over removable media such as USB drives or optical media (CD).
 * Compressed using zip compression to increase storage capacity.
 * Encrypted using tools like PGP
 * Hosted and dynamically updated on a web server for network retrieval.

The protocol does not place any restrictions on how bundles are handled, transported, compressed or encrypted. It does, however, dictate the internal layout of the bundle. Authors of protocol clients must pay special attention to how bundles are created to ensure security and interoperability between client implementations.

## The Structure of a Bundle: Messages

A bundle's message payload is contained in a plaintext file at the root bundle directory. The file is named `messages.pgn`. Messages are joined together via carriage return (`\n`). Typically, `messages.pgn` will contain messages created by the bundle's author, plus messages created by the author's peers.

Here is an example of  the contents of a `messages.pgn` file:

```
author @RZW2HE8MQFRH93NP3YKWC1QGZ6VWDZW1WZPMEKQ5MP0NM6TE54W0.ed25519
kind &ET9C7B9N82XR0F021CXEWSPDH23H4CHMX866WWA3R2PXEFZM67PG.sha256
prev NONE
depth 0
lipmaa 0

a:"b"

signature MYTASNHSPJCV8GH59EEBX27CJHW2N6K02A3MTNFHWHSPDSQKHTMKR23WQS71MTQY0ED4CSK88XNJJ8PV5W9F1BREDR0NZ2CMMRRFT20.sig.ed25519

author @RZW2HE8MQFRH93NP3YKWC1QGZ6VWDZW1WZPMEKQ5MP0NM6TE54W0.ed25519
kind a
prev %XKB8MVA3AZZAG1D29AHPG33G2T8BWAGRKJ0DJ4H01MGPM5R9Y3RG.sha256
depth 1
lipmaa 0

&7Z2CSZKMB1RE5G6SKXRZ63ZGCNP8VVEM3K0XFMYKETRDQSM5WBSG.sha256:"b"

signature 5Y8KQVMJ0BADEPWZEXSMYYVMVE5JYQ0069RQ4S80CT9GA64KCRVTWYSK3V728J024916P9SVZ62W9HVZ189C6PANHWD23T07C779P2R.sig.ed25519

author @RZW2HE8MQFRH93NP3YKWC1QGZ6VWDZW1WZPMEKQ5MP0NM6TE54W0.ed25519
kind a
prev %5N0KJN0TSWRVMXY3JF0FAJRXXDB1AHT9YXBTKE2V7H98GKQND4PG.sha256
depth 2
lipmaa 1

b:&HDDSVC617PS44NP856N3CJN91HPJXEHHHE93595BJC9VJN6KANFG.sha256

signature 7XSFRS28B5T6GN0XEYAASFQQAJG9YTVNSXQY0JD8XHYHPQVNK2VSH081PS9CNPNKEAEJGEPXZR6GSZ21SV1HTKQ7R3SZ49P8PHRER18.sig.ed25519

author @ET6MN0PM5WQKEMPZ3PN39HRFQM8EH2WZRW10W45ZDWV6ZGQ1CWKY.ed25519
kind ab606fa8-958e-45e6-9856-0103217ce0a9
prev %HT23KER8VQJFMFDAWX7RX6CCXVVCZ2H2C9H1HEA5ZNC9A6MAFXG0.sha256
depth 0
lipmaa 2

foo:"f6a627ce-9e0d-4faa-9146-bd4e56b61811"

signature 3NQ3P4J3TJSWH7WTJ0T4WCTVDVX1QAVW31G0K5T59F3X1DRYE3ECD1YVFJ0PT85RTRPD2GG8H091F8TG2A7CV36J8N5Y69RYGTQJE08.sig.ed25519

```

The example above is multiple pigeon messages joined together with a carraige return ("\n").
The file contains the messages of two authors (`@ET6MN...`, `@RZW2H...`).

A peer can use the file above to update their local database. It is important to note that **a client will always reject a message that it cannot verify**. Below is an example of a refused update:

 * Peer A has verified peer B's messages up to `depth 5`.
 * Peer C sends Peer A a new bundle.
 * The bundle contains messages authored by peer B, starting at `depth 8`.
 * Since Peer A cannot verify message at `depth 8` until it receives `depth 7` and `depth 6`, the messages are rejected by peer A.

 * Clients always reject message that cannot be verified. For example, if a `messages.pgn` file starts at `depth 6` for a peer `@ABC`, and the local client has only verified `@ABC`s feed up to `depth 3`, the messages in the bundle will be rejected by the local client because it does not have enough information available to verify the authenticity of the message.
 * Messages must be ordered `depth` for a particular author.
 * Messages

Some things for Pigeon implementors to note about bundles:

 * It can contain other peoples messages
 * It is advisable to only ingest blobs that are referenced

Example:

a user exports a bundle that contains a few messages and the following blobs:

```
&622PRNJ7C0S05XR2AHDPKWMG051B1QW5SXMN2RQHF2AND6J8VGPG.sha256
&FV0FJ0YZADY7C5JTTFYPKDBHTZJ5JVVP5TCKP0605WWXYJG4VMRG.sha256
&YPF11E5N9JFVB6KB1N1WDVVT9DXMCHE0XJWBZHT2CQ29S5SEPCSG.sha256
```

Assuming the user's client follows this specification, the exported bundles strucutre would be laid out as follows on the local filesystem:

```
├── messages.pgn
├── 622PRNJ
│   └── 7C0S05X
│       └── R2AHDPK
│           └── WMG051B
│               └── 1QW5SXM
│                   └── N2RQHF2
│                       └── AND6J8V.GPG
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

# Up Next

This concludes the developer documentation. Please email us with questions. To learn more, continue to the [idea bin](IDEAS.md).
