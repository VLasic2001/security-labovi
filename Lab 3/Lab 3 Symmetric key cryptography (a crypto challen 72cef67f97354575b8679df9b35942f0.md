# Lab 3: Symmetric key cryptography (a crypto challenge)

## Zadatak:

U sklopu vježbe student će riješiti odgovarajući *crypto* izazov, odnosno dešifrirati odgovarajući c*iphertext*. Izazov proizlazi iz činjenice da student nema pristup enkripcijskom ključu.

Za pripremu *crypto* izazova, odnosno enkripciju korištena je Python biblioteka `[cryptography](https://cryptography.io/en/latest/)`. *Plaintext* koji student treba otkriti enkriptiran je korištenjem *high-level* sustava za simetričnu enkripciju iz navedene biblioteke - [Fernet](https://cryptography.io/en/latest/fernet/).

## Crypto challenge:

1. Prvo trebamo iz svog imena i prezimena dobiti enkriptirano ime nase datoteke

```python
from cryptography.hazmat.primitives import hashes

def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

filename = hash('lasic_vedran') + ".encrypted"
```

1. Content datoteke spremimo lokalno pod imenom naseg hashiranog imena
2. Napisemo brute force attack

```python
def brute_force(ciphertext):
    ctr = 0
    while True:
        key_bytes = ctr.to_bytes(32, "big")
        key = base64.urlsafe_b64encode(key_bytes)

        try:
            plaintext = Fernet(key).decrypt(ciphertext)
            header = plaintext[:32]
            if test_png(header):
                print(f"Key: {key}")
                with open("BINGO.png", "wb") as file:
                    file.write(plaintext)
                break
        except Exception:
            pass

        ctr += 1
        if not ctr % 1000:
            print(f"[*] Keys tested: {ctr:,}", end="\r")
```

1. Moramo znati kada smo uspjesno dekriptirali datoteku, kako znamo da je enkriptirana datoteka .png file mozemo provjeriti header svakog pokusaja plaintexta

```python
def test_png(header):
    if header.startswith(b"\211PNG\r\n\032\n"):
        return True
    return False
```

1. Mozemo otvoriti dobijeni dekriptirani .png file

![Untitled](Lab%203%20Symmetric%20key%20cryptography%20(a%20crypto%20challen%2072cef67f97354575b8679df9b35942f0/Untitled.png)