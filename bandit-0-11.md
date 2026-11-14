# Bandit (OverTheWire), livelli 0-11

Bandit è un wargame che insegna la riga di comando Linux attraverso una connessione SSH.
Host: `bandit.labs.overthewire.org`, porta `2220`. Le password dei livelli non sono incluse: qui c'è il ragionamento.

## Livello 0: connessione
**Obiettivo:** accedere al server via SSH.
**Comando:** `ssh bandit0@bandit.labs.overthewire.org -p 2220`
**Cosa fa:** apre una sessione remota come utente `bandit0`. `-p 2220` indica la porta, diversa dalla 22 standard.

## Livello 0 → 1
**Obiettivo:** trovare la password in un file nella cartella home.
**Comandi:** `ls`, `cat readme`
**Cosa fanno:** `ls` elenca i file, `cat` ne stampa il contenuto.

## Livello 1 → 2
**Obiettivo:** leggere un file il cui nome è `-`.
**Comando:** `cat ./-`
**Perché:** senza `./` il trattino viene interpretato come opzione o come standard input. Con `./` diventa il percorso di un file.

## Livello 2 → 3
**Obiettivo:** leggere un file con spazi nel nome.
**Comando:** `cat "spaces in this filename"`
**Perché:** la shell usa gli spazi per separare gli argomenti. Servono virgolette, oppure l'escape con `\`, oppure il completamento con Tab.

## Livello 3 → 4
**Obiettivo:** trovare un file nascosto dentro la cartella `inhere`.
**Comandi:** `cd inhere`, `ls -a`, `cat` sul file nascosto
**Perché:** su Linux i file che iniziano con `.` sono nascosti e `ls -a` li mostra.

## Livello 4 → 5
**Obiettivo:** tra più file, trovare l'unico leggibile dall'uomo.
**Comandi:** `file ./*`, poi `cat` sul file di tipo "ASCII text"
**Perché:** `file` riconosce il tipo di contenuto, così non si aprono a caso file binari. Il `./` serve perché i nomi iniziano con `-`.

## Livello 5 → 6
**Obiettivo:** trovare un file che sia leggibile, di 1033 byte e non eseguibile, in mezzo a molte cartelle.
**Comando:** `find . -type f -size 1033c ! -executable`
**Cosa fa:** `find` cerca per criteri: `-type f` solo file, `-size 1033c` dimensione in byte, `!` nega la condizione seguente.

## Livello 6 → 7
**Obiettivo:** trovare un file in tutto il server, con proprietario `bandit7`, gruppo `bandit6` e dimensione 33 byte.
**Comando:** `find / -user bandit7 -group bandit6 -size 33c 2>/dev/null`
**Cosa fa:** parte dalla radice `/` e filtra per utente, gruppo e dimensione. `2>/dev/null` scarta gli errori "Permission denied", che altrimenti coprono il risultato.

## Livello 7 → 8
**Obiettivo:** trovare la password accanto alla parola "millionth" in un file molto lungo.
**Comando:** `grep millionth data.txt`
**Cosa fa:** `grep` stampa solo le righe che contengono la stringa cercata.

## Livello 8 → 9
**Obiettivo:** trovare l'unica riga che compare una sola volta.
**Comando:** `sort data.txt | uniq -u`
**Perché:** `uniq` confronta solo righe adiacenti, quindi prima si ordina con `sort`. `-u` mostra solo le righe uniche. La pipe `|` passa l'output di un comando al successivo.

## Livello 9 → 10
**Obiettivo:** estrarre la password da un file binario, dove è una delle poche stringhe leggibili, preceduta da alcuni `=`.
**Comando:** `strings data.txt | grep "=="`
**Cosa fa:** `strings` estrae il testo leggibile da un file binario, `grep` filtra le righe con i caratteri `=`.

## Livello 10 → 11
**Obiettivo:** decodificare un file in base64.
**Comando:** `base64 -d data.txt`
**Nota:** base64 è una codifica, non una cifratura: non protegge niente, serve solo a rappresentare dati in forma testuale.

## Livello 11 → 12
**Obiettivo:** leggere un file in cui ogni lettera è stata spostata di 13 posizioni (ROT13).
**Comando:** `cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'`
**Cosa fa:** `tr` sostituisce i caratteri secondo una mappa. Applicando ROT13 due volte si torna al testo originale.

## Concetti ricorrenti
- Pipe (`|`) e redirect (`2>/dev/null`) per combinare comandi.
- Virgolette e `./` per gestire nomi di file particolari.
- `man` e `--help` come prima fonte per capire un comando nuovo.

## Note personali
Il livello su cui ho riscontrato più difficoltà è dall'11 al 12. Ho trovato più complicato del resto la ROT13.
