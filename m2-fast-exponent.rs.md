``` rust
// Fast Exponentiation by Chris Freeman(22AUG2023)

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

// Perform fast exponentiation.
fn fast_exp(mut num: i64, mut pow: i64) -> i64 {
    let mut res: i64 = 1i64;
    while pow > 0 {
		if pow % 2 == 1 {
			res *= num;
		}
		pow /= 2;
		num *= num;
	}
	return res;
}

// Perform fast exponentiation in a modulus.
fn fast_exp_mod(num: i64, mut pow: i64, modulus: i64) -> i64 {
//    println!("{}, {}, {}",num, pow, modulus);
    let mut res  : i64 = 1i64;
    let mut bit = 0i64;	// bit position
    while pow > 0 {
		let e = fast_exp(2i64, bit);
//		println!("pow={} bit={} e={} ", pow, bit, e);
		if pow % 2 == 1 {
			let ee = fast_exp(num, e);
//			print!("fast_exp({},{})={} mod({})={} ", num, e, ee, modulus, ee % modulus);
			res *= ee % modulus;
		}
//		println!("res= {}",res);
		pow /= 2;
		bit += 1;
	}
	res %= modulus;
    return res;
}

// START HERE...
fn main() {
    println!("GCD and LCM test");
    loop {
		let num: i64 = get_i64("Enter number as integer value or -1 to quit: ");
		if num == -1 { break }
		let powr: i64 = get_i64("Enter power integer value: ");
		let modul: i64 = get_i64("Enter modulus integer value: ");
		let mut expd: i64 = num.pow(powr as u32);
		let mut actl = fast_exp(num, powr);
		println!("{} ^ {} = (expected) {} (actual) {}", num, powr, expd, actl);
		expd = (num.pow(powr as u32)) % modul;
		actl = fast_exp_mod(num, powr, modul);
		println!("{} ^ {} % {} = (expected) {} (actual) {}",num, powr, modul, expd, actl);
	}
}


```