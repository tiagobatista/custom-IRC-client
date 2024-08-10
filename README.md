# custom-IRC-client

This challenge is to build your own IRC client. IRC stands for Internet Relay Chat. IRC was one of the earliest network protocols for messaging and multi-party discussions.

To chat, users connect to a server, select a nickname (nick) and message each or directly or join channels to talk to groups of other users.

The IRC protocol also supports groups of servers being connected together and sharing their state. like all Internet protocols it is specified in RFCs. The relevant ones are:

RFC2810 Internet Relay Chat: Architecture. This document describes the overall architecture of IRC.
RFC2811 Internet Relay Chat: Channel Management. This document describes how channels are managed in IRC.
RFC2812 Internet Relay Chat: Client Protocol. This document describes the protocol used between IRC clients and servers.
RFC2813 Internet Relay Chat: Server Protocol. This document describes the “server-server” protocol used between IRC servers in the same network.
We will be implementing a subset of RFC2812 to create a simple IRC client.

The Challenge - Building Your Own IRC Client

This challenge then is to build a simple IRC chat client, you could build that as a desktop application with a GUI (the typical example of this is mIRC) a CLI application, or as a web based application, i.e. freenode offers a web based IRC client here: https://webchat.freenode.net/ which you’ll find useful for testing - you can connect and join a channel then ‘talk’ to your own client.

Step Zero

Please set up your IDE / editor and programming language of choice and proceed directly to Step 1 once you’re ready.

Step 1

In this step your goal is to create your IRC client and have it start and connect to an IRC server. Your client will connect to an IRC server using TCP/IP. For this challenge we will use freenode, so the host will be irc.freenode.net and the port will be 6667.

Once connected you will need to send two initial messages from the IRC protocol:

NICK CCClient
USER guest 0 * :Coding Challenges Client
Feel free to replace CCClient and Coding Challenges Client with you own nickname and ‘real’ name respectively.

You should then read back the messages from the server and for now I suggest you print them out. It will help you begin to understand the IRC protocol.

Step 2

In this step your goals are to parse the welcome messages from the server and to handle PING messages from the server. If your client doesn’t respond to pings the server will shutdown the connection.

The welcome messages fit two patterns, the first looks like this:

:*.freenode.net 002 CCIRC :Your host is *.freenode.net, running version InspIRCd-3
Here the format is prefix ‘:’ followed by server id, then a command/response, 002 in this case, followed by your id then the prefix ‘:’ and some text.

The second is a NOTICE message, that looks like this:

:.freenode.net NOTICE * :* Looking up your ident...
This is again the prefix and server followed by the command NOTICE, which is followed by it’s target and the text of the notice after the second prefix.

If you have successfully connected you should see the freenode message of the day and your client should stay connected and be receiving and responding to PING messages from the server.

Step 3

In this step your goal is to handle joining and leaving a channel.

To do this you will need to develop functionality to parse a JOIN and PART message from the server.

I strongly encourage you to develop your protocol handler using test driven development, here are some test cases to get you started:

:*.freenode.net 353 CCIRC = #cc :@CCIRC
:*.freenode.net NOTICE CCIRC :*** Ident lookup timed out, using ~guest instead.
:CCIRC!~guest@freenode-kge.qup.pic9tt.IP MODE CCIRC :+wRix
:CCIRC!~guest@freenode-kge.qup.pic9tt.IP JOIN :#cc
:CCIRC!~guest@freenode-kge.qup.pic9tt.IP PART :#cc
:Guest4454!~guest@freenode-kge.qup.pic9tt.IP NICK :JohnC
You will want to parse these to extract the origin, command, optional target and parameter from the message.

You will also need to handle user input. Typically the user will input the command /join #cc to join the channel #cc and /part to leave a channel. Once the user has input the command you will need to create and send the appropriate message to the server.

For /join #cc that will be: JOIN #cc

For /part it will be: PART #cc assuming you were on the channel #cc, otherwise put the appropriate channel in there instead.

You will then need to handle the responses back from the server. You can refer to section five of the RFC to understand the response codes.

You will want to track what channel the client has joined/left within your client. For this challenge we are going to assume the user only joins one channel at a time, but most full blown IRC clients allow users to join multiple channels.

Step 4

In this step your goal is to handle nick changes. The user command is usually /nick NewNickname for example, /nick JohnC and the server will respond:

:CCIRC!~guest@freenode-kge.qup.pic9tt.IP NICK :JohnC

You will also get a similar message when another user on a channel that you are on changes their nickname. For example I joined the #cc channel from the web client and change my nickname which resulted in the server sending this message to my client:

:JohnC!~JohnC@freenode-kge.qup.pic9tt.IP NICK :CCTest2

Your client should present the user with a relevant human translation of this change, i.e.:

JohnC is now known as CCTest2

Step 5

In this step your goal is to send and receive messages on a channel. Usually anything the user enters that isn’t prefixed with ‘/’ is chat to be sent to the current channel. So take some user input and send it to their current channel. If they’re not on a channel don’t send it and report an error.

The format for sending a message to a channel is:

PRIVMSG channel :message text

i.e.:

PRIVMSG #cc :Hi everyone! How are you all?

When another user sends a message on the channel the server will send you a message that looks like:

:JohnC!~JohnC@freenode-kge.qup.pic9tt.IP PRIVMSG #cc :Hi There!

Normally a client will parse that and then present the user with a message more like:

JohnC: Hi There!

Once you’ve done that, on to Step 6.

Step 6

In this step your goal is to leave IRC politely. The usual user command to quit IRC is /quit message where the message is optional.

When a user leaves this way the server will send to the other clients a message like this:

:JohnC!~JohnC@freenode-kge.qup.pic9tt.IP QUIT :Quit: Bye bye!

Usually a client will present that as something like this:

JohnC has left IRC Quit: Bye bye!

To tell a server that your user is leaving send the message:

QUIT: message

Implement that and test your client works and then pat yourself on the back, you’ve built a lightweight IRC client.

Going Further

You can take this challenge further by implementing the rest of the IRC Client-Server protocol as specified in the RFC.
