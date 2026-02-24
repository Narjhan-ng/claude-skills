---
name: bug-hunt
description: "Analisi e localizzazione bug: da una descrizione del problema, cerca le cause nel codice, propone diagnosi con livello di confidenza, genera test di riproduzione e fix."
user-invocable: true
argument-hint: "<descrizione del bug>"
---

# Bug Hunt — Analisi e Localizzazione Bug

Da una descrizione del problema (errore, comportamento inatteso, stack trace), analizza il codice per localizzare la causa, propone una diagnosi e guida la risoluzione con un test di riproduzione prima del fix.

**Integra il workflow TDD:** prima il test che riproduce il bug (Red), poi il fix (Green).

**Argomenti:** `<descrizione del bug>` (testo libero, stack trace, o riferimento a un errore)

---

## Flusso Principale

### 1. Raccogli le informazioni sul bug

Analizza l'input dell'utente ed estrai:

- **Sintomo:** Cosa succede? (errore, comportamento inatteso, crash, dato sbagliato)
- **Contesto:** Dove succede? (endpoint, pagina, funzione, comando)
- **Riproduzione:** Quali passi portano al problema? (se forniti)
- **Stack trace:** Se presente, analizzalo immediatamente
- **Aspettativa:** Cosa dovrebbe succedere invece?

Se mancano informazioni critiche, chiedi all'utente **una sola volta** con domande specifiche:
- "In quale endpoint/pagina/funzione si verifica?"
- "C'è uno stack trace o un messaggio di errore?"
- "Il problema è costante o intermittente?"

Non chiedere più di 3 domande. Lavora con quello che hai.

### 2. Localizza la causa

#### Strategia di ricerca

Segui il flusso dei dati partendo dal sintomo e risalendo verso la causa:

1. **Se c'è uno stack trace** — Parti dal frame più specifico (il primo che appartiene al codice del progetto, non a librerie esterne). Leggi il file e la riga indicata con contesto sufficiente.

2. **Se c'è un endpoint/route** — Trova il controller/handler, segui il flusso: validazione → service → repository → risposta.

3. **Se c'è un messaggio di errore** — Cerca il messaggio nel codice per trovare dove viene generato.

4. **Se c'è solo una descrizione comportamentale** — Identifica il modulo responsabile della funzionalità descritta e analizzane la logica.

#### Analisi del codice

Per ogni file rilevante che leggi, cerca:

- **Logica condizionale errata**: condizioni invertite, mancanti, o con operatori sbagliati
- **Gestione null/undefined**: accesso a proprietà di oggetti potenzialmente nulli
- **Off-by-one**: errori su indici, limiti di loop, paginazione
- **Stato mutabile**: variabili condivise modificate in modo inatteso
- **Race condition**: operazioni asincrone non sincronizzate
- **Type mismatch**: confronti tra tipi diversi, conversioni implicite
- **Ordine di esecuzione**: operazioni che assumono un ordine non garantito
- **Dati non validati**: input che arriva in formato inatteso
- **Regressione**: codice recentemente modificato che ha rotto un comportamento

#### Allarga la ricerca se necessario

Se la causa non è evidente nel punto del sintomo:
- Cerca le chiamate alla funzione sospetta (chi la invoca e con quali argomenti?)
- Cerca modifiche recenti ai file coinvolti (`git log` / `git blame`)
- Verifica se il problema è nei dati (query, seed, migrazione) piuttosto che nel codice

### 3. Presenta la diagnosi

```markdown
## Diagnosi: [titolo breve del bug]

### Sintomo
[Cosa succede]

### Causa identificata
**Confidenza:** alta / media / bassa

**File:** [percorso:riga]
**Problema:** [spiegazione precisa di cosa c'è di sbagliato]

[Snippet del codice problematico con annotazione]

### Perché succede
[Spiegazione del flusso: come si arriva a questo punto e perché il codice si comporta così]

### Impatto
- [Quali funzionalità sono affette]
- [Il problema è limitato a questo caso o più ampio?]

### Cause alternative (se confidenza < alta)
- [Altra possibile causa e come verificarla]
```

Chiedi conferma all'utente prima di procedere con il fix.

### 4. Genera il test di riproduzione

**Prima del fix, scrivi il test che dimostra il bug.**

1. Crea un test che riproduce esattamente il comportamento errato:
   - Nome: `test_[funzione]_[scenario_bug]_[comportamento_atteso]`
   - Il test deve **fallire** con il codice attuale (dimostrando il bug)
   - Il test deve **passare** dopo il fix (definendo il comportamento corretto)

2. Struttura AAA:
   ```
   // Arrange — ricrea le condizioni che causano il bug
   // Act — esegui l'operazione che fallisce
   // Assert — verifica il comportamento CORRETTO (quello che DOVREBBE succedere)
   ```

3. Esegui il test e verifica che **fallisca** per la ragione giusta:
   - Se il test passa → il bug non è stato riprodotto, rivedi la diagnosi
   - Se fallisce per una ragione diversa → il test non è accurato, correggilo
   - Comunica: `RED: test di riproduzione fallisce correttamente`

### 5. Applica il fix

1. Modifica **solo** il codice necessario per correggere il bug
2. Non refactorare, non aggiungere feature, non "migliorare" codice circostante
3. Esegui il test di riproduzione — deve passare
4. Esegui **tutti i test del progetto** — nessuna regressione
5. Comunica: `GREEN: bug fixato, test di riproduzione passa, nessuna regressione`

### 6. Report finale

```markdown
## Bug Fix: [titolo]

### Causa
[Una riga: cosa c'era di sbagliato]

### Fix applicato
**File:** [percorso:riga]
[Diff o descrizione della modifica]

### Test aggiunto
**File:** [percorso del test]
- `test_[nome]` — verifica che [comportamento corretto]

### Test suite
- Test di riproduzione: passa
- Test esistenti: tutti passano (X/X)
- Regressioni: nessuna

### Nota
[Se il bug rivela un problema più ampio, segnalalo — es: "Questo pattern
di accesso non validato si ripete in altri 3 file. Considera `/code-review src/services/`"]
```

---

## Gestione Casi Particolari

### Bug intermittente

Se il bug non è costantemente riproducibile:
1. Cerca cause legate a: timing, race condition, stato condiviso, ordine di esecuzione
2. Il test di riproduzione deve forzare la condizione (mock del timing, setup dello stato specifico)
3. Segnala se il test è deterministico o potenzialmente flaky

### Bug in dipendenza esterna

Se la causa è in una libreria/servizio esterno:
1. Segnala chiaramente che il problema non è nel codice del progetto
2. Proponi un workaround nel codice del progetto (validazione, fallback, pin versione)
3. Suggerisci di aprire una issue nella libreria se appropriato

### Causa non trovata

Se dopo l'analisi la causa rimane incerta:
1. **Non inventare una diagnosi.** Comunica onestamente cosa hai trovato e cosa no
2. Presenta le ipotesi ordinate per probabilità
3. Suggerisci passi di debug aggiuntivi:
   - Log specifici da aggiungere
   - Dati da ispezionare
   - Test da eseguire per restringere le cause

---

## Regole Globali

### Cosa questa skill NON fa

- **NON** indovina — se non trova la causa, lo dice
- **NON** fixa senza test di riproduzione (a meno che il test sia tecnicamente impossibile)
- **NON** modifica codice non correlato al bug
- **NON** aggiunge "miglioramenti preventivi" durante il fix

### Principi

- **Minimo intervento**: il fix più piccolo che risolve il problema è il migliore
- **Test first**: il test di riproduzione è obbligatorio, non opzionale
- **Nessuna regressione**: dopo il fix, tutti i test esistenti devono ancora passare
- **Trasparenza**: se la confidenza è bassa, dichiaralo esplicitamente

### Interazione con altre skill

- Il test di riproduzione segue le convenzioni di `/tdd` (naming, struttura AAA, pattern mock)
- Se il bug rivela problemi strutturali → suggerisci `/code-review` o `/refactor`
- Se il bug è in una fase del phase-plan → aggiorna la nota nella fase in STATE.md
- Se il fix richiede modifiche ai contratti → segnala la necessità di aggiornare CONTRACTS.md
