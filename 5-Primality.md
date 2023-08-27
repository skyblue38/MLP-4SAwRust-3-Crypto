# 5. Primality Testing
Methods used previously to locate prime numbers are not sufficient for really massive numbers required by the RSA encryption scheme. So this milestone is about developing methods for testing if a massive number is probably prime.
## WORKFLOW
### 1. Generate random numbers using a PRNG
Re-used the code for *Linear Congruential Generator* from previous projects... see: `struct Prng`
### 2. Test primes using `is_probably _prime`
The code for this function is:
``` rust
// Primality test based on Fermat's little theorem
fn is_probably_prime(prng: &mut Prng, p: i64, num_tests: usize) -> bool {
	for _ in 0..num_tests {
		let next_rand = prng.next_i64(2, p - 1);
		let witness = fast_exp_mod(next_rand, p - 1, p);
		if witness != 1 { return false }
	}
	return true;
}
```
### 3. Generate primes - `find_prime`
The `find_prime` function is shown below:
``` rust
// Probabilistically find a prime number within the range [min, max).
fn find_prime(prng: &mut Prng, min: i64, max: i64, num_tests: i64) -> i64 {
    loop {
		let p = prng.next_i64(min, max -1);
		if p % 2 == 0 { continue }
		if is_probably_prime(prng, p, num_tests as usize) {return p}
	}
}
```
### 4. Write `main` to try out the new functions
The main function was given in the project notes and is shown below:
``` rust
fn main() {
    // Prepare a Prng.
    let mut prng = Prng::new();
    // Display the probability that a number is prime
    // if it passes all NUM_TESTS tests.
    let probability = 1f64 / (2f64.powf(NUM_TESTS as f64));
    println!("Probability: {}%\n", probability*100.0);
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
```

The complete program code can be downloaded here: [5-Primality](5-Primality.md)

Results of compilation and execution are shown below:
``` bash
[sysadmin@centos8s m5_primality]$ cargo run --release
   Compiling m5_primality v0.1.0 (/home/sysadmin/MLP/FSAwRust-3-Crypto/m5_primality)
    Finished release [optimized] target(s) in 0.51s
     Running `target/release/m5_primality`
Probability: 0.000095367431640625%

# Digits (max 9): 1
Prime: 5
# Digits (max 9): 2
Prime: 41
# Digits (max 9): 3
Prime: 241
# Digits (max 9): 4
Prime: 7561
# Digits (max 9): 5
Prime: 67607
# Digits (max 9): 6
Prime: 647641
# Digits (max 9): 7
Prime: 7419437
# Digits (max 9): 8
Prime: 66938749
# Digits (max 9): 9
Prime: 663381967
# Digits (max 9): 10
Prime: 1605088747
# Digits (max 9): 11
^C
[sysadmin@centos8s m5_primality]$ 
```
Note that attempt to get an 11 digit prime caused the program to fall into an interminable loop...

## RESOURCES
* [Wikipedia - Pseudo Random Number Generator](https://en.wikipedia.org/wiki/Pseudorandom_number_generator)
* [Wikipedia - Linear congruential generator](https://en.wikipedia.org/wiki/Linear_congruential_generator)
* [Probabilistically determine whether a number is prime in C#](http://csharphelper.com/howtos/howto_check_primality.html)
* [Find random prime numbers in C#](http://csharphelper.com/howtos/howto_find_primes.html)
* [Wikipedia - Miller-Rabin Primality Test](https://en.wikipedia.org/wiki/Miller%E2%80%93Rabin_primality_test)
* [RosttaCode - Miller-Rabin Primality Test in Rust](https://rosettacode.org/wiki/Miller%E2%80%93Rabin_primality_test#Rust)


### (end)
