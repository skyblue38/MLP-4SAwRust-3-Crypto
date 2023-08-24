# 4. Factoring Numbers
For this milestone, a program that factors numbers will be implemented.
## WORKFLOW
### 1. Research prime factors
See the URLS listed in the **RESOURCES** section below...
?
### 2. Write a `find_factors`
The method uses an iterative approach to first find even factors (ie multiples of 2), then trying all the odd numbers until the candidate factor is greater than the square root of the target number.
``` Rust
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
```
### 3. Write `main` function
A simple version of `main` was prepared just to test the `find_factors` function. The full version is shown later...
### 4. Write `multiply_vector`
``` rust
// Multiply the factors given in a Vector
fn multiply_vector(v:Vec<i64>) -> i64 {
	let mut prod = 1i64;
	for i in 0..v.len() {
		prod *= v[i];
	}
	return prod;
}
```
### 5. Create `primes` vector (upto 100M)
Tis code was added to the `main` function and run at the beginning...
``` rust
    let maxprimes = 100000000usize;
    let sv = sieve_of_eratosthenes(maxprimes);
    let primes = sieve_to_primes(sv);
    println!("Found {} primes up to {}.", primes.len(), maxprimes);
```
### 6. Write `find_factors_sieve`
This function is similar to the `find_factors` function but also takes `primes` as a parameter. Instead of looping through odd numbers (as in `find_factors`), it should loop through the values in the `primes` vector.
``` rust
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
```
### 7. Compare runs using `find_factors` and `find_factors_sieve`
The `main` function that uses both methods of finding the factors is shown here:
``` rust
// START HERE
fn main() {
    println!("Find Factors");
    let maxprimes = 100000000usize;
    let sv = sieve_of_eratosthenes(maxprimes);
    let primes = sieve_to_primes(sv);
    println!("Found {} primes up to {}.", primes.len(), maxprimes);
    loop {
		let n = get_i64("Enter a number (or -1 to exit)");
		if n == -1 { break }
		let v: Vec<i64> = find_factors(n);
		print!("Number {} has factors: {}",n, v[0]);
		for i in 1..v.len() {
			print!("x{}",v[i]);
		}
		let m = multiply_vector(v);
		if n == m {
			println!(" (Correct)");
		} else {
			println!(" (Product of factors: {} not correct!)", m);
		}
		let w: Vec<i64> = find_factors_sieve(n, &primes);
		print!("Number {} has factors: {}",n, w[0]);
		for i in 1..w.len() {
			print!("x{}",w[i]);
		}
		let p = multiply_vector(w);
		if n == p {
			println!(" (Correct)");
		} else {
			println!(" (Product of factors: {} not correct!)", p);
		}

	}
}
```

When run it produces output like this:
``` bash
[sysadmin@centos8s ~]$ cd MLP/FSAwRust-3-Crypto/m4-factoring/
[sysadmin@centos8s m4-factoring]$ cargo run --release
    Finished release [optimized] target(s) in 0.14s
     Running `target/release/m4-factoring`
Find Factors
Found 5761455 primes up to 100000000.
Enter a number (or -1 to exit)312680865509917
Number 312680865509917 has factors: 7791799x40129483 (Correct)
Number 312680865509917 has factors: 7791799x40129483 (Correct)
Enter a number (or -1 to exit)1819448968910731	
Number 1819448968910731 has factors: 40129483x45339457 (Correct)
Number 1819448968910731 has factors: 40129483x45339457 (Correct)
Enter a number (or -1 to exit)12345678901234
Number 12345678901234 has factors: 2x7x73x12079920647 (Correct)
Number 12345678901234 has factors: 2x7x73x12079920647 (Correct)
Enter a number (or -1 to exit)6795742697625173
Number 6795742697625173 has factors: 6880691x987654103 (Correct)
Number 6795742697625173 has factors: 6880691x987654103 (Correct)
Enter a number (or -1 to exit)64374108854777
Number 64374108854777 has factors: 64374108854777 (Correct)
Number 64374108854777 has factors: 64374108854777 (Correct)
Enter a number (or -1 to exit)-1
[sysadmin@centos8s m4-factoring]$ 
```
### 8. Include comparative timing for each function
THe `main` function was modified to include timing calls:
``` rust
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

When the timing code is included the run looks like this:
``` bash
[sysadmin@centos8s ~]$ cd MLP/FSAwRust-3-Crypto/m4-factoring/
[sysadmin@centos8s m4-factoring]$ cargo run --release
   Compiling m4-factoring v0.1.0 (/home/sysadmin/MLP/FSAwRust-3-Crypto/m4-factoring)
    Finished release [optimized] target(s) in 0.68s
     Running `target/release/m4-factoring`
Find Factors
Found 5761455 primes up to 100000000 in 4.349139076s seconds.
Enter a number (or -1 to exit): 6795742697625173
Number 6795742697625173 has factors: 6880691x987654103 in 93.661194ms seconds. (Correct)
Number 6795742697625173 has prime factors: 6880691x987654103 in 13.001007ms seconds. (Correct)
Enter a number (or -1 to exit): 12345678901234
Number 12345678901234 has factors: 2x7x73x12079920647 in 1.260098ms seconds. (Correct)
Number 12345678901234 has prime factors: 2x7x73x12079920647 in 214.004µs seconds. (Correct)
Enter a number (or -1 to exit): 1819448968910731
Number 1819448968910731 has factors: 40129483x45339457 in 516.384051ms seconds. (Correct)
Number 1819448968910731 has prime factors: 40129483x45339457 in 66.067724ms seconds. (Correct)
Enter a number (or -1 to exit): -1
```
Note that the `find_factors_sieve` method is on average about 16% faster than the iterative `find_factors` method, but there is longer start-up time, as it took a bit less than 5 seconds to generate the neraly 6 million prime numbers for the range of numbers up to 100 million.

The rust source code of the final program is available HERE... [m4_find_factors.rs](m4_find_factors.rs.md)
## RESOURCES
* [Find a number’s prime factors in C#](http://csharphelper.com/howtos/howto_list_prime_factors.html)
* [Rosetta Code - Find Factors in Rust](https://rosettacode.org/wiki/Factors_of_an_integer#Rust)
* 
### (END)


