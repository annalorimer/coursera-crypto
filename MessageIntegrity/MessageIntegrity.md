# Message Integrity

- A small break from encryption ...

- **Goal** Integrity, but no confidentiality.
 - ex. Windows OS files. We need to know that they're legit, but they don't need to be confidential

<br>
<br>
<br>

- [Message Authentication Code](#message-authentication-code)
 - [Attacks](#attacks)
- [ECBC-MAC](#ecbc-mac)
- [NMAC](#nmac)
- [MAC Padding](#mac-padding)
- [CMAC](#cmac)
- [PMAC](#pmac)
- [One Time MAC](#one-time-mac)
- [Carter-Wegman MAC](#carter-Wegman-mac)


## Message Authentication Code

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

<br>
<br>
<br>
<br>
<br>

> **Theorem**
>
> If F: K x X -> Y is a secure PRF and 1/|Y| is negligible then I<sub>F</sub> is a secure MAC.
>
> Proof:
>
> Suppose f: K x X -> Y is a truly random. Then a MAC adversary A must win a MAC game. But f(_m_) is independent of _m_. So A has a probability of 1/|Y| of guessing _t_.

ex. PRF F: K x X -> Y and I<sub>F</sub> = (S,V) defined as S(k,m) = F(k,m) and V (k,m,t) = _yes_ if t = F(k,m) and _no_ otherwise.

> **Lemma**
>
> If F: K x X -> {0,1}<sup>n</sup> is a secure PRF then so is F<sub>_t_</sub>(k,m) = F(k,m)[1,..._t_]
>
> Consequence:
> If (S,V) is a MAC baed on a secure PRF that outputs n-bit tags, the truncated MAC outputting w-bit tags is secure as long as 0.5<sup>w</sup> is still negligible.

<br>
<br>
<br>
<br>
<br>

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

 <br>
 <br>

> **Theorem**
>
> For any L > 0, for all efficient q-query PRF  and adversary A attacking F<sub>ECBC</sub> or F<sub>NMAC</sub> there exists an efficient adversary B such that:
>
> ADV<sub>PRF</sub>[A, F<sub>ECBC</sub>] <= ADV<sub>PRF</sub>[B, F] + 2 q<sup>2</sup>/|X| and
>
> ADV<sub>PRF</sub>[A, F<sub>NMAC</sub>] <= q • L • ADV<sub>PRF</sub>[B, F] + q<sup>2</sup>/2|K|
>
> Consequence:
>
> ECBC is secure as long as q << |X|<sup>1/2</sup> and
>
> NMAC is secure as long as q << |K|<sup>1/2</sup>
>
> This means that the key must be changed after a certain amount of time because the Birthday Paradox states that there will be a collision and then then the construction is vulnerable.

<br>
<br>
<br>

## MAC Padding

- What if the message is not a multiple of the block size?

ex. Padding with zeros is not sufficient because for any _m_, Eve obtains the tag for _m_ || 0 since pad(_m_) == pad (_m_ ||0).

- So, our padding function must be invertible.

ex. ISO padding is with 100...00, adding a dummy block if needed.

![ISO Padding](https://github.com/annalorimer/coursera-crypto/blob/master/MessageIntegrity/ISOPadding.png)

- The dummy block is necessary to avoid an `extension attack`
 - In the case that the message ends with 10...00 the attacker can obtain the tag for the message without it's ending => the pad is not invertible


## CMAC

- Using a randomized padding function we can avoid ever having to use a dummy block

- Variant of CBC-MAC where k = (k, k<sub>1</sub>, k<sub>2</sub>)
 - k is used in the standard CBC-MAC algorithm
 - k<sub>1</sub> and k<sub>2</sub> are used in the padding scheme at the end and are derived from k

- If the message length is not a multiple of a block length then ISO padding is appended, but no dummy block is added
 - We also XOR the last block with a secret key k<sub>1</sub>

- If the message length is a multiple of the block length we don't append anything
 - We XOR it is k<sub>2</sub>

- XOR-ing the output of the cascade function with either k<sub>1</sub> or k<sub>2</sub> makes it impossible to apply an extension attack!
 - Because the adversary doesn't know the last block that went in to the function


## PMAC

- All the MACs we've seen so far are not parallelizable
 - So we can't take advantage of multiple cores to compute them

- PMAC is a parallelizable MAC!

![ISO Padding](https://github.com/annalorimer/coursera-crypto/blob/master/MessageIntegrity/PMAC.png)

- P is a masking function that forces order on the blocks
 - If the blocks were interchangable and existentional forgery would be possible

- PMAC is `incremental`
 - You don't necessarily need to recompute every block for a new message

> **Theorem**
>
> For any L > 0 if F is secure over (K, X, X) then F<sub>PMAC</sub> is a secure PRF over (k, X<sup>>=L</sup>, X) and for every efficient q-query PRF adversary A attacking F<sub>PMAC</sub> there exists and efficient adversary B such that
>
> ADV<sub>PRF</sub>[A, F<sub>PMAC</sub>] <= ADV<sub>PRF</sub> + 2 q<sup>2</sup> L<sup>2</sup>/|X|
>
> Consequence:
>
> PMAC is secure as long as q << |X|<sup>1/2</sup>

## One Time MAC

- Analog of one time pad

- Only used for the integrity of one message
 - So Eve can only see one tag-message pair => secure against any Eve

ex. Let q be a large prime.

k = (k, a) in {1, ... , q}<sup>2</sup>

msg = (m[1], ... , m[L])

S(key, msg) = P<sub>msg</sub>(k) + q (mod q)

P<sub>msg</sub>(k) = m[L]x<sup>L</sup> + ... + m[1]x

## Carter-Wegman MAC

- It'd be great if we could make one time MACs many time MACs ...

- Let (S,V) be a secure one-time MAC over (K, M, {0,1}<sup>n</sup>) and let F: K<sub>F</sub> x {0,1}<sup>n</sup> -> {0,1}<sup>n</sup> be a secure PRF.
 - `Carter-Wegman MAC` is defined as CW((k<sub>1</sub>, k<sub>2</sub>), m) = (r, F(k<sub>1</sub>), r) XOR S(k<sub>2</sub>, m) for random r <- {0,1}<sup>n</sup>

> **Theorem**
>
> If (S,V) is a secure one-time MAC and F a secure PRF then CW is a secure MAC outputting tags in {0,1}<sup>n</sup>
