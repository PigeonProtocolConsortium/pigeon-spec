# I Have Internet Access. Why Should I Care?

 * [Everything [in the cloud] is Amazing, But Nothing is Ours](https://alexdanco.com/2019/10/26/everything-is-amazing-but-nothing-is-ours/)
 * [Encryption is Not Preventing Law Enforcement from Investigating Crime](https://www.alec.org/article/encryption-is-not-preventing-law-enforcement-from-investigating-crime/)
 * [‘Nobody’s got to use the Internet’: A GOP lawmaker’s response to concerns about Web privacy](https://www.washingtonpost.com/news/powerpost/wp/2017/04/15/nobodys-got-to-use-the-internet-a-gop-lawmakers-response-to-concerns-about-web-privacy/)
 * [How Iran Turned Off the Internet](https://thewire.in/tech/how-iran-turned-off-the-internet)
 * [Google goes offline after fibre cables cut](https://www.bbc.com/news/technology-50851420)
 * [Building Internet-connected things seems obvious today, but what about when there’s no Internet?](https://back7.co/home/raspberry-pi-recovery-kit)
 * [The death of America's net neutrality and how it affects you](https://www.dw.com/en/the-death-of-americas-net-neutrality-and-how-it-affects-you/a-43934099)
 * [YouTube and Facebook Are Removing Evidence of Atrocities, Jeopardizing Cases Against War Criminals](https://theintercept.com/2017/11/02/war-crimes-youtube-facebook-syria-rohingya/)
 * [Iraq introduces nightly internet curfew](https://netblocks.org/reports/iraq-introduces-nightly-internet-curfew-JAp1DKBd)
 * [Building a Low-Tech Internet](https://www.lowtechmagazine.com/2015/10/how-to-build-a-low-tech-internet.html)
 * [Inside Cuba's massive, weekly, human-curated sneakernet](https://boingboing.net/2018/05/03/inside-cubas-massive-weekly.html)
 * [CollapseOS](https://collapseos.org/)
 * [Indian Internet shut down as protests rage against citizenship bill](https://edition.cnn.com/2019/12/12/asia/india-shutdown-citizenship-bill-intl-hnk/index.html)
 * [Authoritarian Nations Are Turning the Internet Into a Weapon](https://onezero.medium.com/authoritarian-nations-are-turning-the-internet-into-a-weapon-10119d4e9992)
 * [Russian Law Takes Effect that Gives Government Sweeping Power Over Internet](https://www.npr.org/2019/11/01/775366588/russian-law-takes-effect-that-gives-government-sweeping-power-over-internet)

# When is Pigeon the Wrong Choice?

 * When the application requires true deletion of data, ephemeral data or mutability of previously created data. Pigeon feeds are immutable, append-only and permanently replicated by peers.
 * When the application requires realtime interactions or does not benefit from delay tolerance. Support for TCP or UDP sockets is unlikely to ever be added to core libraries.
 * Extremely "chatty" protocols. Pigeon was built with the assumption that data storage is cheap and data transfer is expensive and slow. Use cases with complex handshakes, pinging or timeouts may not be well suited to this protocol.

# Is This a Blockchain?

It's different than a block chain despite some similarity
Pigeon has no singletons. Each participant maintains their own chain.
There is no global blockchain (or any global anything really)
If you trust someone, you agree to replicate their chain (called a "feed")
you also replicate the feed of their peers creating the concept of "listening distance"
This is how Secure Scuttlebutt is achitected, so I can't take any credit for that idea.
The messages can also contain blobs and references to other feeds (like links)
So if I'm friends with you, and you're friends with Tunas, I can browse Tunas' blobs and text entries

# Up Next

This concludes the frequently asked questions section. To learn more, continue to the [roadmap](ROADMAP.md)