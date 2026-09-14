# Cryptography & Security (`hashlib`, `secrets`, `hmac`)

Security vulnerabilities in modern software frequently stem from two mistakes:
1. Using predictable pseudorandom number generators (like `random`) for secrets or tokens.
2. Insecurely storing passwords with fast, unsalted hashes (like raw MD5 or SHA-1) or using variable-time string comparisons that are susceptible to timing attacks.

Python's standard library provides dedicated modules specifically designed to handle cryptographic tasks securely: `hashlib`, `secrets`, and `hmac`.

---

## The Golden Rule: `secrets` vs `random`

| Feature | `random` Module | `secrets` Module |
| :--- | :--- | :--- |
| **Engine** | Mersenne Twister | Operating system CSPRNG (`/dev/urandom`, `CryptGenRandom`) |
| **Cryptographically Secure?** | **No!** Output can be predicted after observing 624 values | **Yes.** Unpredictable |
| **Intended Use** | Simulations, games, statistical modeling | Passwords, security tokens, API keys, session IDs |

!!! danger "Never Use `random` for Security!"
    Never use `random.choice()`, `random.random()`, or `random.randint()` to generate reset tokens, encryption keys, or passwords. Always use `secrets`.

---

## Generating Secure Tokens with `secrets`

Introduced in **Python 3.6 (PEP 506)**, the `secrets` module provides simple functions for generating cryptographically secure values:

```python
import secrets

# Generate URL-safe base64 tokens (ideal for password reset links)
reset_token = secrets.token_urlsafe(32)
print(f"Password reset token: {reset_token}")

# Generate hexadecimal tokens (ideal for API keys)
api_key = secrets.token_hex(16)
print(f"API key: {api_key}")

# Generate raw random bytes (for cryptographic salts or encryption IVs)
salt = secrets.token_bytes(16)

# Cryptographically pick an element from a sequence
dice_roll = secrets.choice(["heads", "tails"])

# Secure integer in range [0, n)
secure_int = secrets.randbelow(100)
```

<div class="terminal-output">
Password reset token: D8m9xV_o1Xq4v2J0bW8y_zL3uP9tA2eR4sT6wY8kI0o
API key: a4f8e12d9b6c0731f82e5d4a1b90cd3f
</div>

---

## Cryptographic Hashing with `hashlib`

A cryptographic hash function takes arbitrary data and produces a fixed-size digest. The hash is deterministic (same input produces same hash), but one-way (computationally infeasible to reverse).

### Common Hash Algorithms

Modern applications should use **SHA-256**, **SHA-512**, **SHA-3**, or **BLAKE2**:

```python
import hashlib

data = b"Antigravity Python Engine"

# SHA-256 hash
sha256_hash = hashlib.sha256(data).hexdigest()
print(f"SHA-256: {sha256_hash}")

# BLAKE2b (faster than SHA-256 with high security)
blake_hash = hashlib.blake2b(data).hexdigest()
print(f"BLAKE2b: {blake_hash}")
```

<div class="terminal-output">
SHA-256: 6fa731bc9d87...
BLAKE2b: e589b21f37ac...
</div>

!!! warning "Avoid MD5 and SHA-1"
    Algorithms like `hashlib.md5()` and `hashlib.sha1()` suffer from known collision attacks and are broken for cryptographic security. Use them only when required for checksum verification of legacy files.

---

## Streaming Hash for Large Files

Never load entire multi-gigabyte files into RAM just to calculate a hash. Stream the file in chunks using `update()`:

=== "Python 3.11+ (Built-in `file_digest`)"
    ```python
    import hashlib

    with open("dataset.iso", "rb") as f:
        digest = hashlib.file_digest(f, "sha256")
        print(f"File SHA-256: {digest.hexdigest()}")
    ```

=== "Python 3.10 and Earlier"
    ```python
    import hashlib

    hasher = hashlib.sha256()
    with open("dataset.iso", "rb") as f:
        # Read in 64KB chunks
        while chunk := f.read(65536):
            hasher.update(chunk)

    print(f"File SHA-256: {hasher.hexdigest()}")
    ```

---

## Password Hashing with PBKDF2

Never store plain text passwords or raw single-pass SHA-256 hashes. Attackers can crack unsalted hashes in seconds using rainbow tables or GPUs.

Use **PBKDF2** (Password-Based Key Derivation Function 2) with a random salt and thousands of iterations to slow down brute-force attempts:

```python
import hashlib
import secrets

def hash_password(password: str) -> tuple[bytes, bytes]:
    # Generate 16 bytes of cryptographically secure random salt
    salt = secrets.token_bytes(16)
    
    # Hash using PBKDF2 with 600,000 iterations of HMAC-SHA256
    key = hashlib.pbkdf2_hmac(
        "sha256",
        password.encode("utf-8"),
        salt,
        iterations=600_000
    )
    return salt, key

def verify_password(stored_salt: bytes, stored_key: bytes, provided_password: str) -> bool:
    new_key = hashlib.pbkdf2_hmac(
        "sha256",
        provided_password.encode("utf-8"),
        stored_salt,
        iterations=600_000
    )
    # Use constant-time comparison to prevent timing attacks!
    import hmac
    return hmac.compare_digest(new_key, stored_key)

# Demonstration:
salt, key = hash_password("SuperSecret123!")
print("Verification with correct password:", verify_password(salt, key, "SuperSecret123!"))
print("Verification with wrong password:  ", verify_password(salt, key, "WrongPassword"))
```

<div class="terminal-output">
Verification with correct password: True
Verification with wrong password:   False
</div>

---

## Preventing Timing Attacks with `hmac.compare_digest`

Standard string comparison (`a == b`) returns `False` immediately upon encountering the first non-matching character. Attackers can measure tiny discrepancies in response time (nanoseconds) to guess valid API keys character by character.

`hmac.compare_digest` takes constant time regardless of where or whether strings differ:

```python
import hmac

stored_api_token = "secret_tok_99182312"
user_provided_token = "secret_tok_00000000"

# SAFE: Constant time comparison
is_valid = hmac.compare_digest(stored_api_token, user_provided_token)
```
