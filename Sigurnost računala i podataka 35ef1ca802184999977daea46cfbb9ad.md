# Sigurnost računala i podataka

## **Lab 4: Message authentication and integrity**

Cilj ove vježbe bio je primjeniti teoretske spoznaje o osnovnim kriptografskim

mehanizmima za autentikaciju i zaštitu integriteta poruka u praktičnom primjeru. Pri tome smo se koristili simetričnim i asimetričnim kriptografskim mehanizmima: *message authentication code (MAC)*, zasnovane na simetričnim ključevima i *digitalne potpise*, zasnovane na javnim ključevima.

### Zadatak 1

Cilj Zadatka 1 je bio zaštita autentičnosti poruke koristeći MAC. Pri tome smo koristili HMAC mehanizam iz Python biblioteke `cryptography` .

Prvo smo smislili plan izvođenja challenge-a:

```python
#Signing process
#1. read file
#2. sign the message content using MAC primitve
#3. store the signature in a separate file

#Verification process
#1. read file
#2. read signature
#3. sign the message content using MAC primitve
#4. compare generated signature with received signature
```

Prvo smo kreirali nekakvu tekstualnu datoteku i u nju smo napisali sadržaj poruke koju želimo zaštiti.

Nakon toga smo generirali MAC, a za to smo koristili funkciju generate_MAC.

```python
def generate_MAC(key, message):
	if not isinstance(message, bytes):
		message = message.encode()

	h = hmac.HMAC(key, hashes.SHA256())
	h.update(message)
	signature = h.finalize()
	return signature
```

Taj generirani MAC kasnije smo spremili u zasebnu datoteku koja se zvala message.sig (sig - signature).

 Nakon toga smo pročitali tekstualnu datoteku u kojoj se nalazi sadržaj poruke, a zatim i datoteku u kojoj se nalazi i generirani MAC.

U funkciji generate_MAC smo generirali MAC lokalno.

Funkciji verify_MAC poslali smo ključ, potpis tj. MAC i poruku. Usporedili smo MAC dobiven ovom funkcijom i prijašnji, lokalno dobiveni MAC. Ako funkcija vraća `true` onda su MAC-ovi jednaki, a u suprotnom vraća `false` i MAC-ovi su različiti.

```python
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

### Zadatak 2.

U drugom zadatku trebali smo utvrditi vremenski ispravan redosljed transakcija dionicama. Autenticirani nalozi transakcija nalazili su se na lokalnom poslužitelju. Sa servera smo preuzeli personalizirane izazove.

Za rješavanje ovog izazova bio nam je potreban program wget.

Za provjeru MAC-a bio nam je potreban ključ, a on je dobiven iz našeg vlastitog imena:

```python
key = "topic_lucija".encode()
```

Tekstualne datoteke nisu se spremile na isto mjesto gdje je bila pohranjena naša skripta, pa smo trebali kreirati path do tog mjesta.

Unutar petlje (for) za svaki par smo pohranili poruku u varijablu message, a MAC u varijablu signature. Kada smo pozvali funkciji verify_MAC provjerili smo autentičnost poruke, a na izlazu smo ispisali sadržaj te poruke i OK/NOK.

Na kraju smo sve autentične poruke u vremenski ispravnom redosljedu spremili u niz koji se zvao authentic_message te smo ih u ispravnom redosljedu na kraju i ispisali u terminalu.