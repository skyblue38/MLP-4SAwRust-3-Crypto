# 2. Fast Exponentiation
This milestone is about implementing functions that perform fast exponentiation and it's application to modulus arithmetic. This is used by the RSA encryption method.

## WORKFLOW
### 1. Write `fast_exp` and `fast_exp_mod` functions
``` rust
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
fn fast_exp_mod(mut num: i64, mut pow: i64, modulus: i64) -> i64 {
//    println!("{}, {}, {}",num, pow, modulus);
    let mut res  : i64 = 1i64;
    let mut bit = 0i64;	// bit position
    let mut e = 1i64;	// power of 2
    let mut ee = 1i64;	// power of num
    while pow > 0 {
		e = fast_exp(2i64, bit);
//		println!("pow={} bit={} e={} ", pow, bit, e);
		if pow % 2 == 1 {
			ee = fast_exp(num, e);
//			print!("fast_exp({},{})={} mod({})={} ", num, e, ee, modulus, ee % modulus);
			res *= (ee % modulus);
		}
//		println!("res= {}",res);
		pow /= 2;
		bit += 1;
	}
	res %= modulus;
    return res;
}

```
### 2. Test the `fast_exp...` functions
Use the `get_i64` function from previous milestone to read three values (`num`, `pow` and `mod`) then compare calculated values of `num ^ pow` and `num ^ pow % mod`.

The `main ` function si shown below:
``` rust
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

The complete program source code is available here: [m2-fast-exponent.rs](m2-fast-exponent.rs.md)

Below is shown some sample runs:
``` bash
[sysadmin@centos8s m2_fast_exponent]$ cargo run --release
   Compiling m2_fast_exponent v0.1.0 (/home/sysadmin/MLP/FSAwRust-3-Crypto/m2_fast_exponent)
    Finished release [optimized] target(s) in 0.51s
     Running `target/release/m2_fast_exponent`
GCD and LCM test
Enter number as integer value or -1 to quit: 15
Enter power integer value: 14
Enter modulus integer value: 13
15 ^ 14 = (expected) 29192926025390625 (actual) 29192926025390625
15 ^ 14 % 13 = (expected) 4 (actual) 4
Enter number as integer value or -1 to quit: 15
Enter power integer value: 14
Enter modulus integer value: 12
15 ^ 14 = (expected) 29192926025390625 (actual) 29192926025390625
15 ^ 14 % 12 = (expected) 9 (actual) 9
Enter number as integer value or -1 to quit: 15
Enter power integer value: 14
Enter modulus integer value: 11
15 ^ 14 = (expected) 29192926025390625 (actual) 29192926025390625
15 ^ 14 % 11 = (expected) 3 (actual) 3
Enter number as integer value or -1 to quit: 16
Enter power integer value: 15
Enter modulus integer value: 14
16 ^ 15 = (expected) 1152921504606846976 (actual) 1152921504606846976
16 ^ 15 % 14 = (expected) 8 (actual) 8
Enter number as integer value or -1 to quit: 16
Enter power integer value: 14
Enter modulus integer value: 13
16 ^ 14 = (expected) 72057594037927936 (actual) 72057594037927936
16 ^ 14 % 13 = (expected) 9 (actual) 9

```

## REFERENCES
* _Khan Academy_- [Fast modular exponentiation](https://www.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/fast-modular-exponentiation)
