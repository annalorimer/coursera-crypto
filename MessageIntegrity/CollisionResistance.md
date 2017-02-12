# Collision Resistance

-[Birthday Attack](#birthday-attack)
 - [The Birthday Paradox](#the-birthday-paradox)

<br>
<br>
<br>

- Let H:M -> T be a hash function with |M| >> |T|. A `collision` is a pair (m<sub>0</sub>, m<sub>1</sub>) in M such that H(m<sub>0</sub>) = H(m<sub>1</sub>)

- H is `collision resistant` if for all explicit efficient algorithms A ADV<sub>CR</sub>[A, H] = Pr[A outputs a collision for H] is negligible.

- Let I = (S,V) be a MAC for short messages over (K, M, T) and let H: M<sup>BIG</sup> -> M.
 - Define `I<sup>BIG</sup> = (S<sup>BIG<sup>, V<sup>BIG</sup>)` over (K, M<sup>BIG</sup>, T) as S<sup>BIG</sup>(k, m) = S(k, H(m)) and V<sup>BIG</sup>(k, m ,t) = V(k, H(m), t)

- So H can be sued to MAC large messages!

> **Theorem**
>
> If I is a secure MAC and H is a collision resistant hash function, then I<sup>BIG</sup> is a secure MAC

- Collision resistance is necessary for security
 - If S<sup>BIF</sup> is insecure under chosen message attacks if H is not collision resistant

## Birthday Attack

- Generic attack for collision-resistant functions

- Let H: M -> {0,1} be a hash function with |M| >> 2<sup>n</sup>. Define the `Birthday Attack` as:
 1. Choose 2<sup>n/2</sup> random messages in M m<sub>i</sub>, ... , m<sub>2<sup>n/2</sup></sub> distinct
 2. compute t<sub>i</sub> = H(m<sub>i</sub>)
 3. Look for collision (t<sub>i</sub>, t<sub>j</sub>) i != j, if not found go back to step 1.

### The Birthday Paradox

> **Theorem**
>
> Let r<sub>1</sub>, ... , r<sub>n</sub> be in {1, ... , B} be independent and identically distributed. When n = 1.2 B<sup>1/2</sup> then Pr[There exists i!=j: r<sub>i</sub> = r<sub>j</sub>]
