UPD, what we know so far:

Packets are not partially sent:

https://stackoverflow.com/questions/28289590/can-udp-packets-be-partially-sent-like-tcp-ones

Explanation:

My best understanding of that is like this, we'd like to send the player state across the network, delta's about their keypress/mouse. Once that gets ready to send, first we say .send( data ) as one piece, eventually that will get to hardware somewhere like router or something which decides how the data is going to get there over the internet, and it see's it's type is UPD protocol, so then it can decide how to split the packet up and send it, but this post is saying that it has to be re-assembled by the other players router, before it gets sent from their router to their PC, which guarantees if you get a packet, it's not going to be broken up. (edited)

---------------------------------------------

Packets may not arrive in order:

https://stackoverflow.com/questions/3745115/ensuring-packet-order-in-udp

Explanation:

If we just accept packets and update their position based on them instantly there may be more rubberbanding due to this.


---------------------------------------------


General Resources:

https://developer.valvesoftware.com/wiki/Source_Multiplayer_Networking
