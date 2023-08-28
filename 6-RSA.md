# 6. RSA
For this milestone section, the RSA cypher scheme will be constructed as a *proof of concept* implementation. Due to the small prime numbers used, this implementation is not suitable for production use, but will use previously tested functions, including:

* `Prng` - Class to generate pseudorandom numbers
* `is_probably_prime` - Returns `true` if a number is probably a prime
* `fast_exp_mod` - Performs fast exponentiation in a modulus
* `find_prime` - Finds a prime number within a range
* `gcd` - Finds the greatest common divisor of two integers*

As well as a test `main` function, the following functions will be written:
* `totient` - calculate the totient for primes `p` and `q`
* `random_exponent` - picks a random public exponent
* `inverse_mod` - finds the inverse of a number in a modulus.

The same program will be used to do PKI functions that generate keys, encryption and decryption.

## WORKFLOW
### 1. Write `totient` function
This reuses the `lcm` and `gcd` functions from milestone#1. Here is the code required:
``` rust
// Calculate Carmichael's totient function λ(n)
// where n = p * q and p and q are prime.
fn totient(p: i64, q: i64) -> i64 {
	return lcm(p-1, q-1);
}
```

### 2. Write `random_exponent` function
``` rust
// Pick a random exponent e in the range [3, λ_n)
// such that gcd(e, λ_n) = 1
fn random_exponent(prng: &mut Prng, λ_n: i64) -> i64 {
	loop {
		let e = prng.next_i64(3, λ_n);
		if gcd(e, λ_n) == 1 { return e };
	}
}
```
### 3. Write `inverse_mod` function
The sample implementation pseudo-code from [Wikipedia - Modular Integers](https://en.wikipedia.org/wiki/Extended_Euclidean_algorithm#Modular_integers) was implemented in Rust as:
``` rust
// Calculate the inverse of a number `a` in a modulus
fn inverse_mod(a: i64, n: i64) -> i64 {
	let mut t = 0i64;
	let mut newt = 1i64;
	let mut r = n;
	let mut newr = a;
	while newr != 0 {
		let quotient = r / newr;
		(t, newt) = (newt, t - quotient * newt);
		(r, newr) = (newr, r - quotient * newr);
	}
	if r > 1 {
		return 0i64;
	}
	if t < 0 {
		t += n;
	}
	return t;
 }
```
## Generate Keys
### 4. Create a `Prng`
reused Prng code from previous Milestones...
### 5. Use `find_prime` to pick two random primes
Reused `find_prime` from *Milestone#5*
### 6. Calculate the public key modulus
Public key modulus (n) is the product of the primes from step 5:
### 7. Calculate *Carmichael's totient*
Carmichael's totient uses the `totient` function based on the primes from step 5.
### 8. Pick random *public key* exponent
Uses the `random_exponent` function to pick a random *public key exponent* `e`
### 9. Calculate inverse modulus of  public_key exponent
Uses the `inverse_mod` function to find `d` which is the inverse of `e` in the modulus `λ_n`
### 10. Display important PKI parameter values
At this stage two runs of the program displayed:
``` bash
RSA Proof-of-Concept implementation

*** Public ***
Public key modulus n:	46942193
Public key exponent e:	2538871

*** Private ***
Primes:			7417,	6329
Carmichael totient λ_n:	5866056
Private key exponent d:	1794127

RSA Proof-of-Concept implementation

*** Public ***
Public key modulus n:	55159477
Public key exponent e:	13460823

*** Private ***
Primes:			9689,	5693
Carmichael totient λ_n:	13786024
Private key exponent d:	5898679
```
Increasing the range of primes  to 1000 ... 100000 produced this output:
``` bash
RSA Proof-of-Concept implementation

*** Public ***
Public key modulus	    n:	320178197
Public key exponent	    e:	132856007

*** Private ***
Primes:				        13799,	23203
Carmichael totient	  λ_n:	160070598
Private key exponent	d:	98730623

RSA Proof-of-Concept implementation

*** Public ***
Public key modulus	    n:	1663295311
Public key exponent	    e:	15344183

*** Private ***
Primes:				        77431,	21481
Carmichael totient	  λ_n:	55439880
Private key exponent	d:	1132367
```
## Encrypt Messages
### 11. Get a number message
Used the `get_i64` function from previous milestones...
### 12. Encrypt the number using Public Key exponent
the code `let c = fast_exp_mod(m, e, n)` produces a cyphertext `c` of the message `m`.
## Decrypt Messages
### 13. Decrypt the message using private exponent
The code `let m_c = fast_exp_mod(c, d, n)` produces the plaintext `m_c` of the cyphertext `c`
###  14. The `main` function
The `main` function of this proof is shown below:
``` rust
fn main() {
    println!("RSA Proof-of-Concept implementation");
    let mut prng = Prng::new();
    let p = find_prime(&mut prng, LOWER_P, UPPER_P, NUM_TESTS);
    let q = find_prime(&mut prng, LOWER_P, UPPER_P, NUM_TESTS);
    let n = p * q;
    let λ_n = totient(p, q);
    let e = random_exponent(&mut prng, λ_n);
    let d = inverse_mod(e, λ_n);
    println!("\n*** Public ***");
    println!("Public key modulus	n:	{}",n);
    println!("Public key exponent	e:	{}",e);
    println!("\n*** Private ***");
    println!("Primes:				{},	{}", p, q);
    println!("Carmichael totient	λ_n:	{}",λ_n);
    println!("Private key exponent	d:	{}",d);
    loop {
		let m = get_i64("\nMessage:	");
		if m < 0 { break };
		let c = fast_exp_mod(m, e, n);
		println!("Cyphertext:	{}", c);
		let m_c = fast_exp_mod(c, d, n);
		println!("Plaintext:	{}",m_c);[m6_rsa.rs](m6_rsa.rs.md)
	}
}
```
Final version of complete program source code is available HERE: [m6_rsa.rs](m6_rsa.rs.md)

A sample run of the complete program is shown below:
``` bash
[sysadmin@centos8s m6-rsa]$ cargo run --release
   Compiling m6-rsa v0.1.0 (/home/sysadmin/MLP/FSAwRust-3-Crypto/m6-rsa)
    Finished release [optimized] target(s) in 0.54s
     Running `target/release/m6-rsa`
RSA Proof-of-Concept implementation

*** Public ***
Public key modulus	    n:	35485883
Public key exponent	    e:	6134293

*** Private ***
Primes:				        6067,	5849
Carmichael totient	  λ_n:	17736984
Private key exponent	d:	12515149

Message:	1234567
Cyphertext:	29681728
Plaintext:	1234567

Message:	7654321
Cyphertext:	30839639
Plaintext:	7654321

Message:	9999999
Cyphertext:	14988768
Plaintext:	9999999

Message:	99999999
Cyphertext:	26668582
Plaintext:	29028233

Message:	-1
[sysadmin@centos8s m6-rsa]$ 
```
Note that in all cases except the last (where some kind of integer overflow may have occurred), the plaintext decrypted from the cyphertext of the message are the same :-)

## REFERENCES
* [Wikipedia - RSA (cryptosystem)](https://en.wikipedia.org/wiki/RSA_(cryptosystem))
* [Wikipedia - Carmichael’s totient function](https://en.wikipedia.org/wiki/Carmichael_function)
* [Wikipedia - Computing multiplicative inverses in modular structures](https://en.wikipedia.org/wiki/Extended_Euclidean_algorithm#Computing_multiplicative_inverses_in_modular_structures)
* [Rosetta Code - RSA](https://rosettacode.org/wiki/RSA_code#Rust)
