# 3.LABORATORIJSKA VJEŽBA

# **SYMMETRIC KEY CRYPTOGRAPHY - A CRYPTO CHALLENGE**

## Introduction

Cilj ove vježbe je bio riješiti odgovarajući crypto izazov, tj. dešifrirati ciphertext u kontekstu simetrične kriptografije. 

Za ovaj izazov plaintext kojeg smo otkrivali bio je enkriptiran pomoću high-level sustava za simetričnu enkripciju iz biblioteke *Fernet. Neke osnovne f-je uključuju:*

**generiranje ključa**

`key = Fernet.generate_key()`

**Inicijalizacija fernet sustava pomoću dobivenog ključa:**

`f = Fernet(key)`

**Enkripcija proizvoljne varijable *plaintext:***

`ciphertext = f.encrypt(plaintext)`

**Dekripcija varijable ciphertext:**

`deciphertext = f.decrypt(ciphertext)`

## Start of the challenge

Prvi zdk je bio pronalazak file-a s našim imenom(imena su bila enkriptirana korištenjem SHA-256 algoritma):

```python
from cryptography.hazmat.primitives import hashes

def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

filename = hash('prezime_ime') + ".encrypted"
```

Korišteni ključevi su bili ograničene entropije (22 bita) što znači da postoji 2^22 mogućih kombinacije koje vrtimo uz pomoć counter-a s time da očekujemo pronalaženje ključa nakon pola vremen tj 2^21

```python
from os import path
import base64

from cryptography.hazmat.primitives import hashes
from crypotography.fernet import fernet

def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

def test_png(header):
    if header.startswith(b"\211PNG\r\n\032\n"):
        return True
    return False

def brute_force(cyphertext):
    ctr = 0
    while True:
        key_bytes = ctr.to_bytes(32, "big")
        key = base64.urlsafe_b64encode(key_bytes)

        # Now initialize the Fernet system with the given key
        # and try to decrypt your challenge.
        # Think, how do you know that the key tested is the correct key
        # (i.e., how do you break out of this infinite loop)?

        try:
            plaintext = Fernet(key).decrypt(cyphertext)
            header = plaintext[:32]
            if test_png(header):
                print(f"Key: [key]")
                with open("BINGO.png", "wb") as file:
                    file.write(plaintext)
                break
        except Exception:
            pass
        ctr += 1
        if not ctr % 1000:
            print(f"[*] Keys tested: {ctr:,}", end="\r")

if __name__ == '__main__':
    filename = hash('prezime:ime') + ".encrypted"

    if not path.exists(filename):
        with open(filename, "wb") as file:
            file.write(b"")
    with open(filename, "rb") as file:
        cyphertext = file.read()

brute_force(cyphertext)
```

Rezultat prethodno navedenog koda bio je PNG file sa našim imenom. Pri pronalasku rješenja za dani izazov od velike koristi je bilo poznavanje činjenica da svaki PNG file ima identičnih prvih 8 bajtova  `\211 P N G \r \n \032 \n`. Na taj smo način znatno suzili izbor ključeva koje moramo provjeravati i ubrzali izvođenje programa. Dakle provjeravamo da li header(prva 32bita plaintexta) počinje sa heksadekadskim vrijednostima 89 50 4E 47 0D 0A 1A 0A i ima li nasa dekripcija smisla. Ako je sve bilo uspješno plaintext će se spremiti u datoteku PNG formata i moći čemo vidjeti sadržaj te datoteke,u našem slučaju slike.