# Sigurnost računala i podataka

## **Lab 3: Symmetric key cryptography (a crypto challenge)**

Cilj treće vježbe je da riješimo *crypto* izazov, tj. da dešifriramo odgovarajući *ciphertext*. Izazov proizlazi iz činjenice da nemamo pristup enkripcijskom ključu.

Za enkripciju korištena je biblioteka `cryptography` . *Plaintext* koji trebamo otkirti enkriptiran je korištenjem *high-level* sustava za simetričnu enkripciju iz biblioteke **Fernet.**

Ovo su koraci koje moramo izvršiti za uspješno rješavanje izazova:

- otvorimo srp-dev-container (za ovo potrebno je znanje iz prošle vježbe)
- saznajemo ime datoteke (personalizirane) koju moramo dešifrirati:

```python
from cryptography.hazmat.primitives import hashes
def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

filename = hash('topic_lucija') + ".encrypted"
```

- nakon toga preuzmemo našu datoteku i spremimo je u folder gdje nam je spremljen i brute_force.py
- za enkripciju smo koristili ključeve ograničene entropije - 22 bita. Ključevi su generirani pomoću Ferneta. Trebamo saznati naš ključ, trebamo dešifrirati naš file, znamo ciphertext i plaintext. Koristimo brute-force metodu. Prolazimo kroz sve ključeve:

```python
def brute_force_attack(ciphertext):
    #print(ciphertext) bila je ovo samo provjera
    ctr = 0
    while True:
        key_bytes = ctr.to_bytes(32, "big")
        key = base64.urlsafe_b64encode(key_bytes)

        #try to use this key somehow
        plaintext = Fernet(key).decrypt(ciphertext)

        header = plaintext[:32]
        if test_png(header):
            print(f"KEY FOUND: {key}")
            with open("BINGO.png", "wb") as file:
                file.write(plaintext)
            break

        ctr += 1
        if not ctr % 1000:
            print(f"[*] Keys tested: {ctr,}", end="\r")
```

- naša enkriptirana datoteka je png formata, stoga moramo preko određene funkcije provjeriti je li datoteka u tom formatu:

```python
def test_png(header):
    if header.startsWith("\211PNG\r\n\032\n"):
        return True
    return False
```

- u terminalu pozovemo `brute_force(ciphertext)`
- na kraju, dobivamo dešifriranu poruku, tj. u našem slučaju sliku, koja izgleda ovako:

//slika posebno stavljena u repozitorij