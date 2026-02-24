---
name: code-review
description: "Review semantica del codice: analizza naming, responsabilità, coerenza con le convenzioni del progetto, edge case non gestiti, accoppiamento. Genera report con severity e fix proposti."
user-invocable: true
argument-hint: "[file|directory|fase N]"
---

# Code Review — Review Semantica del Codice

Analizza il codice per problemi che un linter non può trovare: responsabilità miste, naming debole, accoppiamento nascosto, edge case non gestiti, incoerenza con le convenzioni del progetto.

**Non duplica linter o tool statici** — si concentra su ciò che richiede comprensione semantica del codice e del suo contesto.

**Argomenti:** `<file>` | `<directory>` | `fase <N>`

---

## Flusso Principale

### 1. Determina lo scope della review

In base all'argomento ricevuto:

- **`<file>`** — Review di un singolo file
- **`<directory>`** — Review di tutti i file sorgente nella directory (escludi test, config, asset)
- **`fase <N>`** — Leggi `phases/STATE.md` per identificare la fase, poi fai review di tutti i file prodotti/modificati in quella fase (usa i deliverable dalla fase come guida)
- **Nessun argomento** — Chiedi all'utente cosa vuole far revisionare

### 2. Carica il contesto del progetto

Prima di analizzare il codice, carica le convenzioni di riferimento:

1. **Se esiste `phases/CONTRACTS.md`** — leggilo per: naming, struttura cartelle, interfacce condivise, convenzioni API, schema dati
2. **Se esiste `CLAUDE.md` nella root del progetto** — leggilo per: regole di codice, pattern da seguire, vincoli architetturali
3. **Se nessuno dei due esiste** — usa il codice circostante (stessa directory, file correlati) per inferire le convenzioni

### 3. Analizza il codice

Per ogni file nello scope, valuta le seguenti dimensioni:

#### 3a. Responsabilità e Struttura

- La classe/modulo ha una singola responsabilità chiara?
- Le funzioni fanno una sola cosa? Sono sotto le 50 righe?
- Ci sono funzioni con troppi parametri (> 4)?
- Il livello di astrazione è coerente dentro ogni funzione?
- Ci sono God Object, God Function o feature envy?

#### 3b. Naming e Leggibilità

- I nomi rivelano l'intento? (no `data`, `temp`, `result`, `handle`, `process` generici)
- Le convenzioni di naming sono coerenti con il progetto? (camelCase vs snake_case, prefissi, suffissi)
- I nomi di funzioni descrivono l'azione? (`get_`, `calculate_`, `validate_`, `send_`)
- Ci sono abbreviazioni ambigue o nomi fuorvianti?
- I commenti spiegano il "perché" e non il "cosa"?

#### 3c. Gestione Errori

- Gli errori sono gestiti esplicitamente (no catch vuoti, no `or None` silenzioso)?
- I messaggi di errore sono utili per il debug?
- Le eccezioni sono specifiche (no generic `Exception`)?
- I casi di errore hanno test corrispondenti?

#### 3d. Accoppiamento e Dipendenze

- Le dipendenze sono iniettate o hardcoded?
- Ci sono dipendenze circolari?
- Il modulo dipende da dettagli implementativi di altri moduli?
- L'accesso ai dati è isolato (pattern Repository o equivalente)?

#### 3e. Edge Case e Robustezza

- Input nulli/vuoti/malformati sono gestiti?
- Ci sono race condition potenziali?
- Le collection vuote sono gestite?
- I limiti numerici sono considerati (overflow, divisione per zero)?
- Ci sono assunzioni implicite non validate?

#### 3f. Coerenza con il Progetto

- Il codice segue le convenzioni in CONTRACTS.md?
- La struttura dei file rispetta la struttura cartelle del progetto?
- I pattern usati sono coerenti con il resto della codebase? (es: se il progetto usa Repository, un nuovo modulo non accede al DB direttamente)
- I tipi/interfacce condivise sono usati correttamente?

#### 3g. Sicurezza (solo se rilevante)

- Input utente validato prima dell'uso?
- Query SQL parametrizzate?
- Secrets non hardcodati?
- Dati sensibili non esposti in log o risposte?

### 4. Genera il report

Presenta i risultati raggruppati per severity:

```markdown
## Code Review: [scope]

### Critico (da risolvere)
Problemi che causano bug, vulnerabilità o violazione delle convenzioni del progetto.

- **[FILE:RIGA]** — [Descrizione del problema]
  - **Problema:** [spiegazione concisa]
  - **Fix:** [codice o istruzione specifica]

### Importante (consigliato)
Problemi di design che degradano manutenibilità o leggibilità.

- **[FILE:RIGA]** — [Descrizione]
  - **Problema:** [spiegazione]
  - **Fix:** [suggerimento]

### Suggerimento (opzionale)
Miglioramenti di stile o convenzione non bloccanti.

- **[FILE:RIGA]** — [Descrizione]

### Positivo
Cose fatte bene da mantenere.

- [Aspetto positivo notato]

### Riepilogo
| Dimensione | Valutazione |
|---|---|
| Responsabilità/SRP | buono / da migliorare / critico |
| Naming | buono / da migliorare / critico |
| Gestione errori | buono / da migliorare / critico |
| Accoppiamento | buono / da migliorare / critico |
| Edge case | buono / da migliorare / critico |
| Coerenza progetto | buono / da migliorare / critico |

**Prossimo passo:** [suggerimento — es: "Usa `/refactor src/services/order.py` per applicare i fix importanti"]
```

### 5. Chiedi come procedere

Dopo il report, chiedi all'utente:
- Vuoi che applichi i fix critici? (procedi direttamente con le modifiche)
- Vuoi che applichi tutti i fix? (critici + importanti)
- Vuoi che generi un `/refactor` per le modifiche strutturali?
- Solo informativo, nessuna modifica

---

## Modalità speciale: Review post-fase

Quando invocato con `fase <N>` dopo un `phase-plan exec N`:

1. Leggi `phases/NN-*.md` per capire gli obiettivi della fase
2. Leggi `phases/CONTRACTS.md` per le convenzioni
3. Identifica i file prodotti nella fase (dalla sezione "Deliverable" e "Stato del Mondo all'Uscita")
4. Applica la review standard su quei file
5. Verifica in aggiunta:
   - I deliverable della fase sono tutti presenti?
   - Il codice rispetta i contratti?
   - Lo "Stato del Mondo all'Uscita" corrisponde alla realtà del codice?
6. Segnala discrepanze tra fase pianificata e codice prodotto

---

## Regole Globali

### Cosa questa skill NON fa

- **NON** segnala problemi di formattazione (spazi, indentazione, punto e virgola) — quello è lavoro del linter
- **NON** esegue il codice o i test — analizza solo staticamente con comprensione semantica
- **NON** riscrive il codice autonomamente senza conferma dell'utente
- **NON** valuta performance runtime — per quello servono profiler reali

### Calibrazione della review

- **Sii specifico**: "questa funzione fa X e Y, dovrebbe fare solo X" > "questa funzione fa troppe cose"
- **Proponi il fix**: non solo il problema, ma come risolverlo concretamente
- **Rispetta il contesto**: un prototipo non richiede la stessa rigore di codice di produzione
- **Non essere pedante**: segnala solo ciò che ha impatto reale su manutenibilità, correttezza o leggibilità

### Interazione con altre skill

- Se la review identifica problemi strutturali significativi → suggerisci `/refactor`
- Se la review trova comportamenti non testati → suggerisci `/tdd red`
- Se la review è post-fase → i risultati informano il `phase-plan complete N`
- Se la review trova problemi UI/UX → suggerisci `/ui-ux-check`
