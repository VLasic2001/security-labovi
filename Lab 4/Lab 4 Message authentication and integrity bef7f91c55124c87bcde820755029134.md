# Lab 4: Message authentication and integrity

## Lab

Cilj vježbe je primjeniti teoretske spoznaje o osnovnim kritografskim mehanizmima za autentikaciju i zaštitu integriteta poruka u praktičnom primjerima. Pri tome ćemo koristiti simetrične i asimetrične kriptografske mehanizme: *message authentication code (MAC)* zasnovane na simetričnim ključevima i *digitalne potpise* zasnovane na javnim ključevima.

## **Zadatak 1**

Implementirati zaštitu integriteta sadržaja poruke primjenom odgovarajućeg *message authentication code (MAC)* algoritma. Koristite pri tome HMAC mehanizam iz Python biblioteka `[cryptography](https://cryptography.io/en/latest/hazmat/primitives/mac/hmac/)`

Prvo nam treba kod za računanje MAC vrijednosti

```python
from cryptography.hazmat.primitives import hashes, hmac

def generate_MAC(key, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    signature = h.finalize()
    return signature
```

Za provjeru MAC virjednosti nam treba druga funkcija

```python
from cryptography.hazmat.primitives import hashes, hmac
from cryptography.exceptions import InvalidSignature

def verify_MAC(key, signature, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    try:
        h.verify(signature)
    except InvalidSignature:
        return False
    else:
        return True
```

Pomoću ove dvije funkcije možemo provjeriti jesu li nad datotekom raene promjene

## **Zadatak 2**

**Utvrditi vremenski ispravnu/autentičnu skevencu transakcija (ispravan redosljed transakcija) dionicama**
. Autenticirani nalozi transakcija (primjenom MAC-a) nalaze se na lokalnom poslužitelju:

Nakon što smo preuzeli transakcije lokalno možemo krenuti

Ključ se dobija iz našeg prezimena i imena:

```python
key = "lasic_vedran".encode()
```

Onda postavimo petlju koja prolazi kroz transakcije i ispisuje ih uz potvrdu jesu li uredu ili ne

```python
messages = []
    for ctr in range(1, 11):
        msg_filename = f"order_{ctr}.txt"
        sig_filename = f"order_{ctr}.sig"
        # print(msg_filename)
        # print(sig_filename)

        with open(f"./challenges/g3/lasic_vedran/mac_challenge/{msg_filename}", "rb") as file:
            message = file.read()
        with open(f"./challenges/g3/lasic_vedran/mac_challenge/{sig_filename}", "rb") as file:
            signature = file.read()

        is_authentic = verify_MAC(key, signature, message)
        print(f'Message {message.decode():>45} {"OK" if is_authentic else "NOK":<6}')
        if (is_authentic):
            messages.append(message.decode())

 

    for x in messages:
        print(x)
```

![Untitled](Lab%204%20Message%20authentication%20and%20integrity%20bef7f91c55124c87bcde820755029134/Untitled.png)

I još možemo sortirati samo OK

```python
messages.sort(key=lambda m: datetime.datetime.fromisoformat(
        re.findall(r'\(.*?\)', m)[0][1:-1]))
```

![Untitled](Lab%204%20Message%20authentication%20and%20integrity%20bef7f91c55124c87bcde820755029134/Untitled%201.png)