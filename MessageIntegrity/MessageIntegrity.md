# Message Integrity

- A small break from encryption ...

- **Goal** Integrity, but no confidentiality.
 - ex. Windows OS files. We need to know that they're legit, but they don't need to be confidential

## Message Authentication Code (MAC)

- A `MAC I = (S,V) defined over (K,M,T)` is a pair of algorithms S and V.
 - S(k,m) outputs a `tag`
 - V(k,m,t) validates the `tag-message pair`
 - K := Keyspace, M := Messagespace, T := Tagspace

 ![Basic MAC](https://github.com/annalorimer/coursera-crypto/blob/master/MessageIntegrity/BasicMAC.png)

- **An attacker should not be able to produce a valid tag or valid tag-message pair**

- For a MAC I = (S,V) and and adversary A, we define a `MAC game` as:
 1. The challenger chooses a random key from K
 2. Adversary performs a chosen message attack by submitting _m1_ to the challenger and receives tag _t1_
 3. Once the adversary has received _Q_ tag-message pairs, they attempt an existential forgery by submitting a tag-message pair to V
 4. We say the adversary wins the game if V outputs yes and the tag-message pair submitted by the adversary is fresh

![MAC Game](https://github.com/annalorimer/coursera-crypto/blob/master/MessageIntegrity/MACgame.png)

- **A MAC I = (S,V) is secure if for all efficient adversaries ADV<sub>MAC</sub>[A,I] = Pr[Challenger Outputs 1] is negligible**
 - ex. I = (S,V) where S(k,m) is always 5-bits is not secure because probability of the adversary guess the tag is not negligible
 - ex. I = (S,V) where _m1_ and _m2_ have the same tag is not secure because the adversary can duplicate the tag


### Attacks

- The attackers goal is to commit `existential forgery` by producing a valid tag-message pair.

- This can be done using a `chosen message attack`
 - Eve gives Alice arbitrary messages and Alice computes the tags.

**CRC**

- Basic MAC `checksum` algorithm that detects random errors
- Keyless, so there's no difference between Alice and Eve
- Very easy for attacker to defeat by blocking the message and creating a new tag





> Theorem:
> If F: K x X -> Y is a secure PRF and 1/|Y| is negligible then I<sub>F</sub> is a secure MAC.
>
> Proof:
> Suppose f: K x X -> Y is a truly random. Then a MAC adversary A must win a MAC game. But f(_m_) is independent of _m_. So A has a probability of 1/|Y| of guessing _t_.

ex. PRF F: K x X -> Y and I<sub>F</sub> = (S,V) defined as S(k,m) = F(k,m) and V (k,m,t) = _yes_ if t = F(k,m) and _no_ otherwise.

> Lemma:
> If F: K x X -> {0,1}<sup>n</sup> is a secure PRF then so is F<sub>_t_</sub>(k,m) = F(k,m)[1,..._t_]
>
> Consequence:
> If (S,V) is a MAC baed on a secure PRF that outputs n-bit tags, the truncated MAC outputting w-bit tags is secure as long as 0.5<sup>w</sup> is still negligible.

## ECBC-MAC

- Uses a PRF that takes messages in X = {0,1}<sup>n</sup> and outputs messages in the same set X
- From this PRF we define F<sub>ECBC</sub>: K<sup>2</sup> x X<sup><=L</sup> -> X.
 - F<sub>ECBC</sub> takes pairs of keys and arbitrarily long messages (up to L blocks) that can vary in length

![ECBC](https://github.com/annalorimer/coursera-crypto/blob/master/MessageIntegrity/ECBC.png)

- Without the last encryption step you have RCBC (Raw CBC), which is not secure

## NMAC

- Nested MAC
- Uses PRF F: K x X -> K to construct F<sub>NMAC</sub>: K<sup>2</sup> x X<sup><= L</sup> -> K

![ECBC](https://github.com/annalorimer/coursera-crypto/blob/master/MessageIntegrity/NMAC.png)

- fpad is s fixed pad that maps _t_ in K to an element in X by appending fpad to _t_.
 - But the final F outputs _t_ in K. 
