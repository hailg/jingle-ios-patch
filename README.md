jingle-ios-patch
================

Support using [webrtc-jingle-client](http://github.com/lukeweber/webrtc-jingle-client) and [XMPPFramework](https://github.com/robbiehanson/XMPPFramework) together.

## About
* This project is just some modifications to webrtc-jingle-client project. Because the best XMPP library for iOS that I know is XMPPFramework, so I think it would be great to use them together. 

* The problem I'm trying to solve is each of them use a different socket to connect to XMPP server, so if we use both libraries without any modifications, our application will have 2 opening sockets. This's not a big problem if we don't care about background state. As Apple allows only 1 socket could be connected in a VOIP application, we need to make them use 1 socket only. My choice is to make them use the socket from XMPPFramework.

## What I have done

* I think that's a good idea to explain what I've done. I almost forget everything when writing this, so it will be hard if webrtc-jingle-client changes and we need to integrate again. So make a note is a good idea. Actually, you don't need to read this stuff, just jump to How to install section. And I hope this project will be integrate into webrtc-jingle-client with Luke help so you don't need to care about this project any more. :D

* I'm doing the following:
    * As you know, XMPPFramework use a GCDAsyncSocket. Currently, this socket allow only 1 delegate to handle the received events (message received, connection closed, connected,...). This is not good for us because we need to pass those events to webrtc too. So, I create GCDAsyncSocketMultiDelegate as a proxy. It will contains a list of actual delegates. So, the GCDAsyncSocket will contain GCDAsyncSocketMultiDelegate instance as its delegate. The  GCDAsyncSocketMultiDelegate instance do nothing than just notify the real delegates. To make thing easy with thread concurrency, I create the proxy and set the real proxies as soon as possible.
    * Change clientsignalingthread.cc -> clientsignalingthread.mm, txmpppump.cc -> txmpppump.mm, voiceclient.cc -> voiceclient.mm.
    * Change xmppmessage.h -> xmppmessage2.h. Because XMPPFramework already have a xmppmessage.h file. I don't think this's necessary, but last time I have a compile problem because of this.
    * Change XmppStream init. Assign GCDAsyncSocketMultiDelegate singleton to its GCDAsyncSocket.
    * Create a new XmppClient named IOSXmppClient. I need to do this because the current XmppClient create its own socket. IOSXmppClient will process messages and events from GCDAsyncSocket. To make this happen, it need to be a GCDAsyncSocketDelegate. So I make a new class named XmppClientDelegate extends GCDAsyncSocketDelegate and contains our IOSXmppClient instance. So currently, the events will follow the path: GCDAsyncSocket -> GCDAsyncSocketMultiDelegate -> XmppClientDelegate -> IOSXmppClient -> webrtc stuffs.
    * Make XmppClientDelegate extends XmppStreamDelegate. We need authenticated events.
    * Change TXmppPump, VoiceClient: use IOSXmppClient instead of XmppClient.
    * Expose signal_thread_ to the outside. We need to post some message from XMPPFramework to this thread. I still did not figure out any better way to do this.
    * Change XmppEngineImpl, make login_task_ to NULL. We need to do this because I left login task to XMPPFramework.

## How to install

1. You should begin by cloning 2 projects:
    * [webrtc-jingle-client](http://github.com/lukeweber/webrtc-jingle-client):
At this time, when I'm writing this. I have a problem, and this is how I [solve it](https://github.com/lukeweber/webrtc-jingle-client/issues/74). Make sure to do every steps that Luke told us.
    * [XMPPFramework](https://github.com/robbiehanson/XMPPFramework):
Just do a normal git clone.

2. Make your own project. This task takes quite a long time, you should see how voiceclient project is organized and do the same thing to your project. Make sure to include enough libaries and some compile flags. To make thing simple, I will ignore this step and use the voiceclient project myself. If you are going to copy third_party folder to your project, make sure you don't change its name. It will be painful to update the build settings of those projects. One more thing, please change your project files to use .mm extension instead of .m. This will help us a lot because we need to call some C++ code.

3. Add XMPPFramework to your project. This quite easy, just follow the guide from their site.

4. After those steps, you should make a build, just make sure everything works well.





