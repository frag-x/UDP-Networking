This repo is for experimentation with UPD networking for frag-x.
It should be used to create MWE's to solve the different challenges which come
with the UPD protocol.

Also since we are starting to work with box2d as our physics engine we should
make MWE's using this.

TODO: 

- Watch this video: https://www.youtube.com/watch?v=6WmK9qa2KIg 

- Bring in client code which grabs player movement 
    - Send that data with the UPD protocol

- On the server swap out our current physics implementation with box2d
    - this means that all of the hitscan and weapons will have to be redone,
      that's fine, for now we'll just focus on movement and re-implement when we
      get there.

- Draw what we get back from the server using interpolation which means, instead
  of drawing whatever data you get instantly you wait a little bit, where
  "little" means you have at least a few packets waiting to be rendered on the
  client, then after say 100ms you start drawing at a rate which allows there
  always to be at least 2 game states waiting to be rendered, thus we can
  interpolate (linearly) between these two game states.
    - Since the player is reacting 100ms after the event actually took place, if
      we were to just send that to the server then it would appear that the
      player has very slow reactions, thus we need to time-stamp every player
      state that goes over the network.
    - When the server gets the information that the player moved out of the way
      it will first take the time stamp and subtract it then recreated the game
      at that time, to see that the player didn't actually die. 
        - How would that work if during that time the server actually thought
          the player died and sent the message to the player that they are dead,
          then after 100ms, realized they weren't dead?


---------------------------------------------

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
