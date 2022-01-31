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
    - For the next example let's pretend there are two players one of which is
      you, and the enemy has just shot directly at your head, let's say that you
      were able to predict that fact that they were going to fire and you could
      move out of the way in time.
    - When the server gets the information that the player moved out of the way
      it will first take the time stamp and subtract it then recreated the game
      at that time, to see that the player didn't actually die.
        - During that 100ms the game must have thought that the player died
          though, since there wasn't any movement, but then after we got their
          position we realized that they dodged the shot and are no longer dead,
          how would the game deal with this?
        - The enemy player would see the enemy get shot and then they wouldn't
          die, this is one of the downsides to this approach.


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
