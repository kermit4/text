TCP is for simple short lived conversations between two peers, like uploading or downloading a file, with some reasonable guesses as to how you would like to handle packet loss.

The prime "feature" of TCP is that it delays messages.

Use UDP in the following cases:
- you plan to continue or retry the conversation after IP changes or long silences, as you now need to address retransmission, sequence, acknowledgements, and timeouts, at a larger time granularity, which would leave two competing and duplicated systems if TCP was still used, which only result in artificial delays.

- you want data as its received, not artifically delayed by the recipient's operating system if it arrives before other data.
- you want to communitate with many peers at once in a synchronized fasion
- your data is time sensitive, such that a lost packet should remain so after a certain amount of time
- your network has a retransmission algorithm such that a peer may receive a message from others if not timely received from you
- the assumptions of your desired reaction to packet loss in TCP do not match your preferences


It is a rare case in fact where a modern application does not handle acknowledgement, retransmission, and ordering, at larger timescales, so it is incorrect to say this is handled by TCP, but that it is, in the common case, duplicated.  Unless high throughput is needed to offload this onto the operating system or hardware, then latency sensitive applicaions will maintain more control at no additional cost.

It is also incorrect to say TCP guarantees ordering and retransmission.  It only guarantees ordering if an application does not retry a new connection after a TCP session failure.   So, if an application does retry, then it has duplicated or even defeated those features of TCP.

TCP is from a simple era when applications would simply pass a session failure up to the user, losing messages, disconnecting and often silently, so there was no duplication at the application level for retransmission.  Data sent was raw without any metadata, i.e. not messages but streams.   Modern applications retransmit and reconnect, duplicating the features of TCP, so it is now redundant and only increases delay and reduces control and visibility.

TCP is for streams, not messages.

TCP does do a few other things, most notably addressing packet sizes, but in the real world you can just use 1500 bytes everywhere, and the very rare networks that aren't 1500 bytes will fragment for you anyway.

TCP's timers are independent, by specification, so when messaging thousands of peers, your own traffic is competing with itself, versus if it had awareness and control of the timing itself.

With TCP the application does not have clear visibility into either the packet loss, or the latency, when speaking with a peer.  This information is very relevant to deciding which peers to communitae with, in a widely distributed application.

In many use cases you have multiple conversations or streams happenning that should not share a single flow control / retransmission delays.  A bulk transfer should not cause latency for a separate unrelated conversation.  A single TCP session is inappropriate in scenarios where there are multiple conversations occuring,   Only a single stream of sequentially accessed data should exist per TCP session, othherwise messages are delayed for reasons not applicable to them.  I.e. if I'm moving furniture from one house to another, I don't need my water bill to wait for that.  That's a different conversation.  It should have its own queue.   

TCP is UDP plus a lot of things.  Those things need to be justified, and if they are duplicated in the application logic too, which tends to be the natural evolution, the continued use of those thhings at both layers needs to be justified.  Rather than migrating the retransmission to the application, developers duplicated it then never bother to remove the generic use case implementation in TCP, by switching to UDP once it's finished to desired parameters at the application layer.

For example, imagine a case of two data centers connected by a VPN incorrectly using TCP.  One TCP stream within that TCP connection causes lately for all other operations.  This is not desired.
