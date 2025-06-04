We are having some issues with the `wc_HmacSetKey` function.

One issue that we had is the address of the Hmac struct, which we need to properly do Hmac


We tried to manually set the Hmac struct to a fixed address. (first 0xdeadbeef to see if it would work)

![[hmac set to 2000000.png]]

manually setting the Hmac ![[hmac location0.png]]