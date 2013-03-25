jingle-ios-patch
================

Support using webrtc-jingle-client and xmppframework together.

This project is just some patch files to webrtc-jingle-client project (http://github.com/lukeweber/webrtc-jingle-client). Because the best XMPP library for iOS that I know is XMPPFramework (https://github.com/robbiehanson/XMPPFramework), so I think it would be great to use them together. 
The problem I'm trying to solve is each of them use a different socket to connect to XMPP server, so if we use both libraries without any modifications, our application will have 2 opening sockets. This's not a big problem if we don't care about background state. As Apple allows only 1 socket could be connected in a VOIP application. 
My solution is modify webrtc-jingle-client a little bit to make it use the same socket of XMPPFramework.
