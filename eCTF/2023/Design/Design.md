Goal: Follow all these security requirements

**SR1:** A car should only unlock and start when the user has an authentic fob that is paired with the car  
Although it may seem obvious, you don’t want anyone to have access to the car when they don’t have the right fob.

**SR2:** Revoking an attacker’s physical access to a fob should also revoke their ability to unlock the associated car  
If you lend your car to someone (e.g., a friend or a valet), they will have physical access to your paired fob. Attackers shouldn’t be able access to your car even after they have returned the original fob.

**SR3:** Observing the communications between a fob and a car while unlocking should not allow an attacker to unlock the car in the future  
The communications between a fob and the car would be visible to attackers, so snooping attackers shouldn’t be able to unlock your car later.

**SR4:** Having an unpaired fob should not allow an attacker to unlock a car without a corresponding paired fob and pairing PIN  
Without both the paired fob and associated PIN, an attacker should not be able to pair a new fob with the car. Otherwise, an attacker with just the fob or just the PIN could make their own fob. Additionally, an attacker with access to the fob should not be able to determine the PIN, as they would then have both components to properly pair a second fob.

**SR5:** A car owner should not be able to add new features to a fob that did not get packaged by the manufacturer  
These features must not be able to be tampered to prevent an owner from adding a feature they have not paid for and to prevent an attacker from escalating privileges on a feature-limited fob.

**SR6:** Access to a feature packaged for one car should not allow an attacker to enable the same feature on another car  
For example, if your neighbor paid for an upgraded feature and the manufacturer sends them the upgrade, you should not be able to add that feature to your car without requesting it from the manufacturer.


