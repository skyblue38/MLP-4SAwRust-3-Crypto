# 1. GCD and LCM
Implementing RSA requires several calculations involving numbers in general and prime numbers in particular. One of those operations is finding the greatest common divisor (GCD) of two numbers.

This project will also explore a method to calculate least common multiples (LCMs), although these are not for the RSA algorithm

This milestone is about how to calculate GCDs and LCMs. The _greatest common divisor_ of A and B is sometimes written GCD(A, B) and is the largest whole number that divides evenly into both A and B. For example, GCD(12, 18) is 6 because 6 is the largest whole number that divides evenly into both 12 and 18.

## WORKFLOW
### 1. Write `gcd` function
``` rust
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
```
### 2. Write `lcm` function
``` rust
// Given two integers, find the Lowest Common Multiple
fn lcm(a: i64, b: i64) -> i64 {
	let g: i64 = (a * b) / gcd(a, b);
	return g
```
}
### 3. Write `get_i64` function
Reused code from project specification
``` rust
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
```
### 4. Build a `main` function
This should loop so the user can enter values A and B on separate lines. If the user enters two values that are at least 1, calculate and display the GCD and LCM.
``` rust
fn main() {
    println!("GCD and LCM test");
    loop {
		let a: i64 = get_i64("Enter first integer value or -1 to quit: ");
		if a == -1 { break }
		let b: i64 = get_i64("Enter second integer value: ");
		println!("GCD({}. {}) = {}",a, b, gcd(a, b));
		println!("LCM({}, {}) = {}",a, b, lcm(a, b));
	}
}
```
### Test run
The complete program is available here: [GCD-LCM.rs](GCD-LCM.rs.md)

Some test runs with known correct results are shown below::
``` bash
[sysadmin@centos8s ~]$ cd MLP/FSAwRust-3-Crypto/m1-GCD-LCM/
[sysadmin@centos8s m1-GCD-LCM]$ cargo run --release
   Compiling m1-GCD-LCM v0.1.0 (/home/sysadmin/MLP/FSAwRust-3-Crypto/m1-GCD-LCM)
warning: crate `m1_GCD_LCM` should have a snake case name
warning: `m1-GCD-LCM` (bin "m1-GCD-LCM") generated 1 warning
    Finished release [optimized] target(s) in 0.58s
     Running `target/release/m1-GCD-LCM`
GCD and LCM test
Enter first integer value or -1 to quit: 270
Enter second integer value: 192
GCD(270. 192) = 6
LCM(270, 192) = 8640
Enter first integer value or -1 to quit: 7469
Enter second integer value: 2464
GCD(7469. 2464) = 77
LCM(7469, 2464) = 239008
Enter first integer value or -1 to quit: 55290
Enter second integer value: 115430
GCD(55290. 115430) = 970
LCM(55290, 115430) = 6579510
Enter first integer value or -1 to quit: -1
[sysadmin@centos8s m1-GCD-LCM]$ 
```
## RESOURCES
* [Khan Academy - The Euclidean Algorithm](https://www.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/the-euclidean-algorithm)
* 

### (end)
