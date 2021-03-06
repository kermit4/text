TCP is for simple short lived conversations between two peers, like loading or downloading a file, with some reasonable guesses as to how you would like to handle packet loss.

The prime "feature" of TCP is that it delays messages.

Use UDP in the following cases:
- you plan to continue or retry the conversation after IP changes or long silences, as you now need to address retransmission, sequence, acknowledgements, and timeouts, at a larger time granularity, which would leave two competing and duplicated systems if TCP was still used, which only result in artificial delays.

- you want data as its received, not artifically delayed by the recipient's operating system if it arrives before other data.
- you want to communitate with many peers at once in a synchronized fasion
- your data is time sensitive, such that a lost packet should remain so after a certain amount of time
- your network has a retransmission algorithm such that a peer may receive a message from others if not timely received from you
- the assumptions of your desired reaction to packet loss in TCP do not match your preferences


It is a rare case in fact where a modern application does not handle acknowledgement, retransmission, and ordering, at larger timescales, so it is incorrect to say this is handled by TCP, but that it is, in the common case, duplicated.  Unless high throughput is needed to offload this onto the operating system or hardware, then latency sensitive applicaions will maintain more control at no additional cost.

It is also incorrect to say TCP gauruntees ordering and retransmission.  It only gauruntees ordering if an application does not retry a new connection after a TCP session failure.   So, if an application does retry, then it has duplicated or even defeated those features of TCP.

TCP is from a simple era when applications would simply pass a session failure up to the user, networks were local so failures were less common than the internet scenario, and data sent was raw without any metadata, i.e. not messages but streams.

TCP is for streams, not messages.

TCP does do a few other things, most notably addressing packet sizes, but in the real world you can just use 1500 bytes everywhere, and the very rare networks that aren't 1500 bytes will fragment for you anyway.

TCP's timers are independent, by specification, so when messaging thousands of peers, your own traffic is competing with itself, versus if it had awareness and control of the timing itself.

With TCP the application does not have clear visibility into either the packet loss, or the latency, when speaking with a peer.  This information is very relevant to deciding which peers to communitae with, in a widely distributed application.
