---
name: bug-hunt
description: "Analisi e localizzazione bug: da una descrizione del problema, cerca le cause nel codice, propone diagnosi con livello di confidenza, genera test di riproduzione e fix."
user-invocable: true
argument-hint: "<descrizione del bug>"
---

# Bug Hunt — Analisi e Localizzazione Bug

Da una descrizione del problema, localizza la causa, propone diagnosi e guida la risoluzione con test di riproduzione prima del fix (Red → Green).

**Argomenti:** `<descrizione del bug>` (testo libero, stack trace, riferimento errore)

---

## Flusso

### 1. Raccogli informazioni

Dall'input estrai: sintomo, contesto (endpoint/pagina/funzione), passi di riproduzione, stack trace, aspettativa. Se mancano info critiche, chiedi **max 3 domande** specifiche. Lavora con quello che hai.

### 2. Localizza la causa

**Strategia di ricerca** (segui il flusso dati dal sintomo alla causa):
- Stack trace → parti dal frame più specifico nel codice del progetto
- Endpoint/route → segui: controller → service → repository → risposta
- Messaggio errore → cercalo nel codice
- Descrizione comportamentale → identifica il modulo responsabile

**Cosa cercare:** logica condizionale errata, null/undefined, off-by-one, stato mutabile, race condition, type mismatch, ordine esecuzione, dati non validati, regressioni recenti

**Se non evidente:** cerca chi invoca la funzione, `git log`/`git blame` per modifiche recenti, verifica se il problema è nei dati

### 3. Presenta diagnosi

Includi: sintomo, causa identificata (file:riga + spiegazione), confidenza (alta/media/bassa), flusso che porta al bug, impatto, cause alternative se confidenza < alta. Chiedi conferma prima del fix.

### 4. Test di riproduzione

Prima del fix, scrivi il test:
- Nome: `test_[funzione]_[scenario_bug]_[comportamento_atteso]`
- Pattern AAA: ricrea condizioni → esegui operazione → verifica comportamento CORRETTO
- Deve fallire con codice attuale, passare dopo il fix
- Se passa subito → bug non riprodotto, rivedi diagnosi

### 5. Applica fix

1. Modifica solo il codice necessario — no refactoring, no feature extra
2. Esegui test riproduzione → deve passare
3. Esegui tutti i test → nessuna regressione
4. Report finale: causa, fix (file:riga + diff), test aggiunto, stato suite

### 6. Casi particolari

**Bug intermittente:** cerca timing/race condition/stato condiviso. Forza la condizione nel test. Segnala se potenzialmente flaky.

**Bug in dipendenza esterna:** segnala che non è nel codice progetto. Proponi workaround (validazione, fallback, pin versione).

**Causa non trovata:** NON inventare. Presenta ipotesi ordinate per probabilità. Suggerisci: log specifici, dati da ispezionare, test per restringere cause.

---

## Regole

- NON indovina — se non trova la causa, lo dice
- NON fixa senza test di riproduzione
- NON modifica codice non correlato al bug
- Minimo intervento: il fix più piccolo è il migliore
- Nessuna regressione: tutti i test esistenti devono passare
