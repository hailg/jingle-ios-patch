jingle-ios-patch
================

Support using [webrtc-jingle-client](http://github.com/lukeweber/webrtc-jingle-client) and [XMPPFramework](https://github.com/robbiehanson/XMPPFramework) together.

## About
* This project is just some modifications to webrtc-jingle-client project. Because the best XMPP library for iOS that I know is XMPPFramework, so I think it would be great to use them together. 

* The problem I'm trying to solve is each of them use a different socket to connect to XMPP server, so if we use both libraries without any modifications, our application will have 2 opening sockets. This's not a big problem if we don't care about background state. As Apple allows only 1 socket could be connected in a VOIP application, we need to make them use 1 socket only. My choice is to make them use the socket from XMPPFramework.

## How to install

1. You should begin by cloning 2 projects:
    * [webrtc-jingle-client](http://github.com/lukeweber/webrtc-jingle-client):
At this time, when I'm writing this. I have a problem, and this is how I [solve it](https://github.com/lukeweber/webrtc-jingle-client/issues/74). Make sure to do every steps that Luke told us.
    * [XMPPFramework](https://github.com/robbiehanson/XMPPFramework):
Just do a normal git clone.

2. Make your own project. This task takes quite a long time, you should see how voiceclient project is organized and do the same thing to your project. Make sure to include enough libaries and some compile flags. To make thing simple, I will ignore this step and use the voiceclient project myself. If you are going to copy third_party folder to your project, make sure you don't change its name. It will be painful to update the build settings of those projects. One more thing, please change your project files to use .mm extension instead of .m. This will help us a lot because we need to call some C++ code.

3. Add XMPPFramework to your project. This quite easy, just follow the guide from their site.

4. After those steps, you should make a build, just make sure everything works well.

5. 



