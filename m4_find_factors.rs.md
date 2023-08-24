``` rust
// Find Factors by Chris Freeman (24AUG23)

use std::io;
use std::io::Write;
use std::time::{Instant};

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

// Build a sieve of Eratosthenes.
fn sieve_of_eratosthenes(max: usize) -> Vec<bool> {
    let mut sva = vec![true; max];
    for i in 2..max {	// check every number upto max
		if sva[i] {		// if prime...
			for j in (2*i..max).step_by(i) {	// visit all multiples
				sva[j] = false;					// and set NOT prime
			}
		}
	}
	return sva;
}

// Convert the sieve into a vector holding prime numbers.
fn sieve_to_primes(sieve: Vec<bool>) -> Vec<i64> {
    let m = sieve.len();
    let mut sv = Vec::<i64>::new();
    for i in 2..m {
		if sieve[i] {
			sv.push(i as i64);
		}
	}
	return sv;
}

// Find factors
fn find_factors(mut num: i64) -> Vec<i64> {
	let mut factors: Vec<i64> = Vec::new(); // new vector of factors
	while num % 2 == 0 {
		factors.push(2i64);
		num /= 2;
	}
	let mut fac = 3i64;
	while (fac * fac) <= num {
		if num % fac == 0 {
			factors.push(fac);
			num /= fac;
		} else {
			fac += 2i64;
		}
	}
	if num > 1i64 {
		factors.push(num);
	}
	return factors	//returns the vactor of factors
}

// Find prime factors
fn find_factors_sieve(mut num: i64, primes: &Vec<i64>) -> Vec<i64> {
	let mut factors: Vec<i64> = Vec::new();
	for i in 0..primes.len() {
		let fac = primes[i];
		if (fac * fac) > num { break }
		if num % fac == 0 {
			factors.push(fac);
			num /= fac;
		}
	}
	if num > 1i64 {
		factors.push(num);
	}
	return factors;
}

// Multiply the factors given in a Vector
fn multiply_vector(v:Vec<i64>) -> i64 {
	let mut prod = 1i64;
	for i in 0..v.len() {
		prod *= v[i];
	}
	return prod;
}

// START HERE
fn main() {
    println!("Find Factors");
    let start0 = Instant::now();
    let maxprimes = 100000000usize;
    let sv = sieve_of_eratosthenes(maxprimes);
    let primes = sieve_to_primes(sv);
    let duration0 = start0.elapsed();
    println!("Found {} primes up to {} in {:?} seconds.", primes.len(), maxprimes, duration0);
    loop {
		let n = get_i64("Enter a number (or -1 to exit): ");
		if n == -1 { break }
		let start1 = Instant::now();
		let v: Vec<i64> = find_factors(n);
		let duration1 = start1.elapsed();
		print!("Number {} has factors: {}",n, v[0]);
		for i in 1..v.len() {
			print!("x{}",v[i]);
		}
		print!(" in {:?} seconds.",duration1);
		let m = multiply_vector(v);
		if n == m {
			println!(" (Correct)");
		} else {
			println!(" (Product of factors: {} not correct!)", m);
		}
		let start2 = Instant::now();
		let w: Vec<i64> = find_factors_sieve(n, &primes);
		let duration2 = start2.elapsed();
		print!("Number {} has prime factors: {}",n, w[0]);
		for i in 1..w.len() {
			print!("x{}",w[i]);
		}
		print!(" in {:?} seconds.",duration2);
		let p = multiply_vector(w);
		if n == p {
			println!(" (Correct)");
		} else {
			println!(" (Product of factors: {} not correct!)", p);
		}

	}
}
```