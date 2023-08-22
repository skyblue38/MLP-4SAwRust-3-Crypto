# 3. Sieve of Eratosthenes
RSA relies heavily on prime numbers so it’s important that primes can be identified... Unfortunately RSA requires very large primes so a number sieve isn’t practical for RSA.

## WORKFLOW
### 1. Build `sieve_of_eratothenes`function.
Build a `sieve_of_eratosthenes` function. It should take as a parameter the largest number that it will consider. It should return a vector of `bool` where `array[p]` is true if `p` is prime.
``` rust
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
```

### 2. Write `print_sieve`
Write a `print_sieve` function that loops through a vector returned by the `sieve_of_eratosthenes` function. It should print prime numbers separated by spaces.
``` rust
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
```
### 3. Write `sieve_to_primes`
Write a `sieve_to_primes` function that converts a vector returned by the `sieve_of_eratosthenes` function into a vector that holds the sieve’s prime numbers.
``` rust
// Print the vector of numbers.
fn print_numbers(primes: &mut Vec<i64>) {
    for prime in primes {
        print!("{prime} ");
    }
    println!();
}
```
### 4. Write `print_numbers`
Write a `print_numbers` function that prints the numbers in a vector

### 5. Write `main`
Use the following main program to test your function.
``` rust
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

### 6. Verify program output
The complete working program source code is available HERE: [m3-sieve.rs](m3-sieve.rs.md)

Verify that the program displays the following output when `max` is 100. Expect to see:
``` text
Max: 100
2 3 5 7 11 13 17 19 23 29 31 37 41 43 47 53 59 61 67 71 73 79 83 89 97
2 3 5 7 11 13 17 19 23 29 31 37 41 43 47 53 59 61 67 71 73 79 83 89 97 
```

Here is the actual output:
``` bash
[sysadmin@centos8s m3-sieve]$ cargo run --release
   Compiling m3-sieve v0.1.0 (/home/sysadmin/MLP/FSAwRust-3-Crypto/m3-sieve)
    Finished release [optimized] target(s) in 0.57s
     Running `target/release/m3-sieve`
Enter Max: 100
Max: 100
2 3 5 7 11 13 17 19 23 29 31 37 41 43 47 53 59 61 67 71 73 79 83 89 97 
2 3 5 7 11 13 17 19 23 29 31 37 41 43 47 53 59 61 67 71 73 79 83 89 97 
[sysadmin@centos8s m3-sieve]$
```


## RESOURCES
* [Use the Sieve of Eratosthenes to find prime numbers in C#](http://csharphelper.com/howtos/howto_sieve_of_eratosthenes.html)
* [Prime number theorem](https://en.wikipedia.org/wiki/Prime_number_theorem)
* [Sieve of Eratosthenes in Rust](https://rustp.org/number-theory/sieve-of-eratosthenes/)
