# Message Integrity

- A small break from encryption ...

- **Goal** Integrity, but no confidentiality.
 - ex. Windows OS files. We need to know that they're legit, but they don't need to be confidential

## Message Authentication Code (MAC)

- A `MAC I = (S,V) defined over (K,M,T)` is a pair of algorithms S and V.
 - S(k,m) outputs a `tag`
 - V(k,m,t) validates the `tag-message pair`
 - K := Keyspace, M := Messagespace, T := Tagspace

 ![Basic MAC](BasicMac.png "BasicMac")

- **An attacker should not be able to produce a valid tag or valid tag-message pair**

- For a MAC I = (S,V) and and adversary A, we define a `MAC game` as:
 i. The challenger chooses a random key from K
 ii. Adversary performs a chosen message attack by submitting _m1_ to the challenger and receives tag _t1_
 iii. Once the adversary has received _Q_ tag-message pairs, they attempt an existential forgery by submitting a tag-message pair to V
 iv. We say the adversary wins the game if V outputs yes and the tag-message pair submitted by the adversary is fresh

![MAC Game](MACGame.png "BasicMac")

- **A MAC I = (S,V) is secure if for all efficient adversaries ADV<sub>MAC</sub>[A,I] = Pr[Challenger Outputs 1] is negligible**
 - ex. I = (S,V) where S(k,m) is always 5-bits is not secure because probability of the adversary guess the tag is not negligible
 - ex. I = (S,V) where _m1_ and _m2_ have the same tag is not secure because the adversary can duplicate the tag


### Attacks

- The attackers goal is to commit `existential forgery` by producing a valid tag-message pair.

- This can be done using a `chosen message attack`
 - Eve gives Alice arbitrary messages and Alice computes the tags.


### CRC

- Basic MAC `checksum` algorithm that detects random errors
- Keyless, so there's no difference between Alice and Eve
- Very easy for attacker to defeat by blocking the message and creating a new tag
