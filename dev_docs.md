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
