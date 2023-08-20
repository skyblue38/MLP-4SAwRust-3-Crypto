``` rust
// Calulate GCD and LCM by Chris Freeman(20AUG2023)

use std::io;
use std::io::Write;

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