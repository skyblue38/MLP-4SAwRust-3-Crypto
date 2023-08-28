``` rust
// proof of Concept version of RSA crypto system by Chris Freeman (28AUG23)


use std::io;
use std::io::Write;
use std::time::{SystemTime, UNIX_EPOCH};

const NUM_TESTS: i64 = 20;
const LOWER_P: i64 = 1000;
const UPPER_P: i64 = 10000;

//Given two integers, find the Greatest Common Divisor
fn gcd(mut a: i64, mut b: i64) -> i64 {
	if a < 0 { a = 0 - a }
	if b < 0 { b = 0 - b }
	if a < b { (a, b) = (b, a) }
	if a == 0 { return b }
	if b == 0 { return a }
	let c = a % b;
	return gcd(b, c)
}

// Given two integers, find the Lowest Common Multiple
fn lcm(a: i64, b: i64) -> i64 {
	let g: i64 = (a * b) / gcd(a, b);
	return g
}

// Calculate Carmichael's totient function λ(n)
// where n = p * q and p and q are prime.
fn totient(p: i64, q: i64) -> i64 {
	return lcm(p-1, q-1);
}

// Pick a random exponent e in the range [3, λ_n)
// such that gcd(e, λ_n) = 1
fn random_exponent(prng: &mut Prng, λ_n: i64) -> i64 {
	loop {
		let e = prng.next_i64(3, λ_n);
		if gcd(e, λ_n) == 1 { return e };
	}
}

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

// ************
// *** Prng ***
// ************
struct Prng {
	seed: u32,
}

impl Prng {
	fn new() -> Self {
		let mut prng = Self { seed: 0 };
		prng.randomize();
		return prng;
	}
	fn randomize(&mut self) {
		let millis = SystemTime::now()
			.duration_since(UNIX_EPOCH)
			.expect("Time went backwards")
			.as_millis();
		self.seed = millis as u32;
	}
	// Return a pseudorandom value in the range [0, 2147483647].
	fn next_u32(&mut self) -> u32 {
		self.seed = self.seed.wrapping_mul(1_103_515_245).wrapping_add(12_345);
		self.seed %= 1 << 31;
		return self.seed;
	}
	// Return a pseudorandom value in the range [0.0, 1.0).
	fn next_f64(&mut self) -> f64 {
		let f = self.next_u32() as f64;
		return f / (2147483647.0 + 1.0);
	}
	// Return a pseudorandom value in the range [min, max).
	fn next_i64(&mut self, min: i64, max: i64) -> i64 {
		let range = (max - min) as f64;
		let result = min as f64 + range * self.next_f64();
		return result as i64;
	}
}

// Prompt the user for an i64.
fn get_i64(prompt: &str) -> i64 {
	print!("{prompt}");
	io::stdout().flush().unwrap();
    let mut str_value = String::new();
    io::stdin()
		.read_line(&mut str_value)
		.expect("Error reading input");
	let trimmed = str_value.trim();
	return trimmed.parse::<i64>()
		.expect("Error parsing integer");
}

// Perform fast exponentiation in a modulus.
fn fast_exp_mod(mut num: i64, mut pow: i64, modulus: i64) -> i64 {
    let mut res = 1i64;
    while pow > 0 {
		if pow % 2 == 1 {
			res = (res * num) % modulus;
		}
		pow /= 2;
		num = (num * num) % modulus;
	}
	return res;
}

// Primality test based on Fermat's little theorem
fn is_probably_prime(prng: &mut Prng, p: i64, num_tests: usize) -> bool {
	for _ in 0..num_tests {
		let next_rand = prng.next_i64(2, p - 1);
		let witness = fast_exp_mod(next_rand, p - 1, p);
		if witness != 1 { return false };
	}
	return true;
}

// Probabilistically find a prime number within the range [min, max).
fn find_prime(prng: &mut Prng, min: i64, max: i64, num_tests: i64) -> i64 {
    loop {
		let p = prng.next_i64(min, max -1);
		if p % 2 == 0 { continue }
		if is_probably_prime(prng, p, num_tests as usize) {return p};
	}
}

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
		println!("Plaintext:	{}",m_c);
	}
}
```