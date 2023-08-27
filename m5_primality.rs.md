``` rust
// Primality Testing by Chris Freeman (27AUG23)

use std::io;
use std::io::Write;
use std::time::{SystemTime, UNIX_EPOCH};

const NUM_TESTS: i64 = 20;

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
//    println!("{}, {}, {}",num, pow, modulus);
    let mut res = 1i64;
    while pow > 0 {
//		println!("num= {}, pow= {}, res= {}",num, pow, res);
		if pow % 2 == 1 {
			res = (res * num) % modulus;
		}
		pow /= 2;
		num = (num * num) % modulus;
	}
//	println!("num= {}, pow= {}, res= {}",num, pow, res);
    return res;
}

// Primality test based on Fermat's little theorem
fn is_probably_prime(prng: &mut Prng, p: i64, num_tests: usize) -> bool {
	for _ in 0..num_tests {
		let next_rand = prng.next_i64(2, p - 1);
		let witness = fast_exp_mod(next_rand, p - 1, p);
		if witness != 1 { return false }
	}
	return true;
}

// Probabilistically find a prime number within the range [min, max).
fn find_prime(prng: &mut Prng, min: i64, max: i64, num_tests: i64) -> i64 {
    loop {
		let p = prng.next_i64(min, max -1);
		if p % 2 == 0 { continue }
		if is_probably_prime(prng, p, num_tests as usize) {return p}
	}
}

fn main() {
    // Prepare a Prng.
    let mut prng = Prng::new();

    // Display the probability that a number is prime
    // if it passes all NUM_TESTS tests.
    let probability = 1f64 / (2f64.powf(NUM_TESTS as f64));
    println!("Probability: {}%\n", probability);

    // Generate random primes.
    loop {
        // Get the number of digits.
        let num_digits = get_i64("# Digits (max 9): ");
        if num_digits < 1 { break; }

        // Calculate minimum and maximum values.
        let mut min = 10i64.pow((num_digits - 1) as u32);
        let max = 10 * min;
        if min == 1 { min = 2; } // 1 is not prime.

        // Find a prime.
        println!("Prime: {}", find_prime(&mut prng, min, max, NUM_TESTS));
    }
}

//fn main() {
//    println!("Primality Testing");
//   	let mut prng = Prng::new();
//    let candidate = get_i64("Enter a number to be tested: ");
//    let repeats = get_i64("Enter number of repeats: ");
//    let prob = 1f64 / (2f64.powf(repeats as f64));
//    let primal = is_probably_prime(&mut prng, candidate, repeats as usize);
//    if primal {
//		println!("{} is a prime number with probability {:?}", candidate, prob);
//	} else {
//		println!("{} is not prime!",candidate);
//	}
//}
```