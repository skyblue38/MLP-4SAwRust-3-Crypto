``` rust
// SIEVE OF ERATOSTHENES by Chris Freeman (22AUG2023)

use std::io;
use std::io::Write;

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

// Print out the primes in the sieve.
fn print_sieve(sieve: &mut Vec<bool>) {
	let m = sieve.len();
	println!("Max: {}", m);
    for i in 2..m {
		if sieve[i] {
			print!("{} ", i);
		}
	}
	println!();
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

// Print the vector of numbers.
fn print_numbers(primes: &mut Vec<i64>) {
    for prime in primes {
        print!("{prime} ");
    }
    println!();
}

fn main() {
    let max = get_i64("Max: ");
    let mut sieve = sieve_of_eratosthenes(max as usize);
    if max < 1000 {
        print_sieve(&mut sieve);
    }
    let mut primes = sieve_to_primes(sieve);
    if max < 1000 {
        print_numbers(&mut primes);
    }
}
```