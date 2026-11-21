# Settimana 2: Bandit (OverTheWire) livelli 12-15 e Python con file e stringhe

Stato: livelli 12-15 completati, livello 16 ancora aperto. Le password dei livelli non sono incluse.

## Livello 12 → 13: hexdump e compressioni annidate
**Obiettivo:** `data.txt` è un hexdump di un file compresso più volte.
**Comandi:**
- `mktemp -d`, poi `cp data.txt` e `cd` nella cartella temporanea: la home non è scrivibile.
- `xxd -r data.txt > data`: riconverte l'hexdump nel file binario originale.
- `file data`: riconosce il tipo di compressione dal contenuto.
- `mv data data.gz` e `gzip -d data.gz`, oppure `bzip2 -d`, oppure `tar xf`, a seconda del tipo.

Si ripete il ciclo `file` e decompressione finché compare "ASCII text".
**Concetti:** un hexdump è la rappresentazione testuale dei byte. `file` guarda il contenuto, non l'estensione. `gzip` e `bzip2` richiedono l'estensione giusta nel nome.

## Livello 13 → 14: chiave SSH privata
**Obiettivo:** non c'è una password, ma un file `sshkey.private` per entrare come `bandit14`.
**Comando:** `ssh -i sshkey.private bandit14@localhost -p 2220`
**Concetti:** una chiave privata dimostra chi sei al posto della password. `localhost` indica la macchina su cui sei già. Una volta dentro, la password del livello è in `/etc/bandit_pass/bandit14`, leggibile con `cat`.

## Livello 14 → 15: inviare la password a una porta
**Obiettivo:** inviare la password del livello alla porta 30000 di `localhost`.
**Comando:** `cat /etc/bandit_pass/bandit14 | nc localhost 30000`
**Cosa fa:** `nc` (netcat) apre una connessione TCP e invia il testo. Il server risponde con la password successiva.

## Livello 15 → 16: la stessa cosa, ma cifrata (SSL/TLS)
**Obiettivo:** inviare la password alla porta 30001, che accetta solo connessioni cifrate.
**Comando:** `cat /etc/bandit_pass/bandit15 | openssl s_client -connect localhost:30001 -quiet -ign_eof`
**Concetti:** `nc` invia in chiaro. `openssl s_client` stabilisce una connessione TLS prima di inviare i dati. `-quiet` evita che certi caratteri vengano interpretati come comandi dal client.

## Livello 16: problema ancora aperto
**Obiettivo:** tra le porte 31000-32000 di `localhost`, trovare quella con SSL che restituisce una chiave privata per il livello successivo.
**Cosa ho provato:** accesso da `localhost` e non, uso di una cartella temporanea, controllo del contenuto del file della chiave e dei permessi.
**Risultato:** il file della chiave risultava vuoto (0 righe), quindi il problema è a monte, nella scelta della porta o nella risposta del server.
**Prossimi passi:**
- Elencare le porte aperte con `nmap -sV -p 31000-32000 localhost`.
- Provare ogni porta con SSL e guardare l'output grezzo.
- Automatizzare la prova con un ciclo `for` in Bash.

## Python: file e stringhe
**Concetti:**
- `open(nome, modo)` con modi `"r"` (lettura), `"w"` (scrittura, sovrascrive) e `"a"` (aggiunge).
- `with` chiude il file automaticamente.
- Metodi di stringa utili: `split()`, `lower()`, `strip()`.

**Esempio minimo:** contare le parole di un file.
```python
with open("prova.txt") as f:
    testo = f.read()
print(len(testo.split()))
```

**Il mio script:** 
def contaparole(nomefile):
    with open(nomefile,"r") as f:
        contents=f.read()
        parole=contents.split()
    return len(parole)
