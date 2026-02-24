---
name: tdd
description: "Workflow Test-Driven Development: genera test dai requisiti PRIMA del codice, poi guida il ciclo Red→Green→Refactor. Supporta unit, integration, E2E con pattern per mock e fixtures."
user-invocable: true
argument-hint: "[feature <descrizione> | red | green | refactor | status | coverage]"
---

# TDD — Test-Driven Development Skill

Forza il workflow TDD autentico: **scrivi i test prima, poi il codice minimo per farli passare, poi migliora**. Funziona con qualsiasi linguaggio e framework.

**Uso standalone** per progetti senza phase-plan, o per feature isolate.
Se il progetto usa `/phase-plan`, il TDD è già integrato in `exec N` — usa questa skill solo per aggiungere test a codice esistente o per feature fuori dal piano fasi.

**Argomenti:** `feature <descrizione>` | `red` | `green` | `refactor` | `status` | `coverage`

---

## Principi Fondamentali

1. **Mai scrivere codice di produzione senza un test che fallisce**
2. **Il test definisce il comportamento atteso**, non il codice esistente
3. **Il codice minimo** per far passare il test, niente di più
4. **Refactor solo quando i test sono verdi**
5. **I test sono documentazione vivente** dei requisiti

---

## Modalità: `feature <descrizione>`

Punto di ingresso principale. Analizza i requisiti di una feature e genera la suite di test completa PRIMA di qualsiasi codice di implementazione.

### Istruzioni

1. **Rileva il contesto del progetto** (esegui una sola volta, poi ricorda):
   - Linguaggio e framework (da file di configurazione: package.json, composer.json, requirements.txt, go.mod, Cargo.toml, pom.xml, ecc.)
   - Test runner in uso (Jest, PHPUnit, pytest, Go test, RSpec, JUnit, Vitest, ecc.)
   - Struttura test esistente (directory, naming convention, helper/fixture già presenti)
   - Pattern di mock/stub già usati nel progetto
   - Se non esiste un test runner configurato, suggerisci quello standard per lo stack e chiedi conferma

2. **Analizza i requisiti della feature.** Dalla descrizione fornita, estrai:
   - **Comportamenti attesi** (happy path)
   - **Edge case** (input vuoti, null, limiti, formati invalidi)
   - **Casi di errore** (eccezioni, codici di errore, fallback)
   - **Interazioni con dipendenze esterne** (DB, API, file system, servizi)
   - **Effetti collaterali** (eventi emessi, notifiche, log, cache)

3. **Presenta il piano dei test all'utente** prima di scrivere qualsiasi file:

   ```
   ## Feature: [nome]

   ### Unit Tests (70%)
   - [ ] test_[cosa]_[scenario]_[risultato_atteso]
   - [ ] test_[cosa]_[scenario]_[risultato_atteso]

   ### Integration Tests (20%)
   - [ ] test_[componenti]_[interazione]

   ### E2E Tests (10%) — se applicabile
   - [ ] test_[flusso_utente]

   ### Mock/Stub necessari
   - [Dipendenza] → [strategia di mock]

   ### Fixtures necessarie
   - [Dato di test] → [dove e come]
   ```

   Chiedi conferma o modifiche.

4. **Solo dopo approvazione**, genera i file di test seguendo queste regole:

   #### Struttura dei test (Pattern AAA)
   Ogni test deve seguire Arrange-Act-Assert con separazione chiara:
   ```
   // Arrange — prepara dati e dipendenze
   // Act — esegui l'azione da testare
   // Assert — verifica il risultato atteso
   ```

   #### Naming dei test
   Usa il pattern: `test_[unità]_[scenario]_[risultato_atteso]`
   - `test_createUser_withValidData_returnsUser`
   - `test_createUser_withDuplicateEmail_throwsConflictError`
   - `test_calculateDiscount_withExpiredCoupon_returnsZero`

   #### Ordine di generazione
   1. **Unit test** — una funzione/metodo alla volta, mock completo delle dipendenze
   2. **Integration test** — interazione reale tra componenti (DB, servizi interni)
   3. **E2E test** — solo se la feature ha un flusso utente end-to-end

   #### Pattern Mock/Stub (adatta al linguaggio del progetto)

   **Dipendenze esterne (API, DB, filesystem):**
   - Mock l'interfaccia, non l'implementazione
   - Usa il sistema di mock nativo del test runner quando possibile
   - Crea factory/builder per oggetti di test complessi

   **Fixtures:**
   - Dati di test in file/funzioni dedicate, mai inline se riusati
   - Factory pattern per creare entità con default sensati
   - Ogni test deve essere indipendente (setup/teardown proprio)

5. **Esegui i test** e verifica che **falliscano tutti** (fase Red).
   - Se un test passa senza codice di produzione, il test è sbagliato — segnalalo
   - Mostra output con il conteggio: `RED: X test falliti su X totali ✓`

6. **Scrivi nel file `TDD_STATUS.md`** nella root del progetto (crea se non esiste):

   ```markdown
   # TDD Status: [Feature]

   ## Fase corrente: 🔴 RED

   | Test | Tipo | Stato |
   |------|------|-------|
   | test_xxx | unit | 🔴 red |
   | test_yyy | unit | 🔴 red |
   | test_zzz | integration | 🔴 red |

   ## Prossimo passo
   Esegui `/tdd green` per implementare il codice minimo.
   ```

---

## Modalità: `red`

Aggiunge nuovi test per comportamenti non ancora coperti, riportando il progetto in stato Red.

### Istruzioni

1. Leggi `TDD_STATUS.md` per capire lo stato corrente.
2. Chiedi all'utente quale comportamento aggiuntivo vuole testare.
3. Genera i nuovi test seguendo le stesse regole di `feature`.
4. Esegui i test e verifica che i nuovi falliscano.
5. Aggiorna `TDD_STATUS.md`.

---

## Modalità: `green`

Implementa il codice **minimo** per far passare i test attualmente in rosso.

### Istruzioni

1. **Leggi `TDD_STATUS.md`** per identificare i test in rosso.

2. **Leggi i file di test** per capire esattamente cosa ci si aspetta.

3. **Implementa il codice di produzione** seguendo queste regole ferree:
   - Scrivi **solo** il codice necessario per far passare i test
   - **NON** aggiungere funzionalità extra, gestione errori non testata, o ottimizzazioni
   - **NON** implementare casi che non hanno un test corrispondente
   - Se serve un file nuovo, crea solo ciò che serve ai test
   - Se un test richiede un'interfaccia/tipo, crea quella esatta
   - Preferisci l'implementazione più semplice e diretta possibile (anche "stupida")

4. **Esegui i test** dopo ogni modifica significativa.
   - Obiettivo: tutti i test devono passare
   - Se un test passa ma un altro si rompe, fix prima di procedere
   - Mai modificare un test per farlo passare (a meno che il test sia genuinamente sbagliato)

5. **Quando tutti i test passano**, aggiorna `TDD_STATUS.md`:

   ```markdown
   ## Fase corrente: 🟢 GREEN

   | Test | Tipo | Stato |
   |------|------|-------|
   | test_xxx | unit | 🟢 green |
   | test_yyy | unit | 🟢 green |

   ## Prossimo passo
   Esegui `/tdd refactor` per migliorare il codice mantenendo i test verdi.
   ```

6. **Se un test è impossibile da far passare** senza cambiare il design previsto, segnala all'utente e proponi alternative.

---

## Modalità: `refactor`

Migliora il codice di produzione e dei test, mantenendo tutti i test verdi.

### Istruzioni

1. **Leggi `TDD_STATUS.md`** — questa modalità è disponibile solo se lo stato è 🟢 GREEN.
   Se lo stato è RED, avvisa l'utente di completare prima la fase green.

2. **Analizza il codice scritto nella fase green** cercando:
   - Duplicazione di codice (DRY)
   - Nomi poco espressivi
   - Funzioni troppo lunghe (> 50 righe)
   - Responsabilità multiple in una classe/funzione
   - Magic numbers/strings
   - Pattern di design mancanti dove appropriato
   - Complessità inutile

3. **Analizza i test** cercando:
   - Test duplicati o ridondanti
   - Setup ripetuto estraibile in fixture/helper
   - Assert poco chiari
   - Test fragili (dipendenti da dettagli implementativi)

4. **Presenta il piano di refactoring** all'utente:
   ```
   ## Refactoring proposto

   ### Codice di produzione
   - [file]: [cosa migliorare] → [come]

   ### Test
   - [file]: [cosa migliorare] → [come]

   ### Non tocco
   - [cosa e perché]
   ```

   Chiedi conferma.

5. **Applica il refactoring** in piccoli passi:
   - Dopo ogni modifica, **esegui tutti i test**
   - Se un test fallisce, annulla l'ultimo cambiamento
   - Mai cambiare comportamento durante il refactoring

6. **Aggiorna `TDD_STATUS.md`**:

   ```markdown
   ## Fase corrente: 🔵 REFACTORED

   ## Refactoring applicati
   - [descrizione del miglioramento]

   ## Prossimo passo
   Feature completata. Usa `/tdd feature <nuova>` per la prossima,
   oppure `/tdd red` per aggiungere test alla feature corrente.
   ```

---

## Modalità: `status`

Mostra lo stato TDD corrente del progetto.

### Istruzioni

1. Leggi `TDD_STATUS.md`. Se non esiste, comunica che nessun ciclo TDD è attivo.
2. Mostra:
   - Feature in lavorazione
   - Fase corrente (Red / Green / Refactor)
   - Conteggio test per stato e tipo
   - Prossima azione suggerita

---

## Modalità: `coverage`

Analizza la copertura dei test rispetto al codice di produzione.

### Istruzioni

1. Identifica il tool di coverage disponibile nel progetto (istanbul/c8, phpunit --coverage, pytest-cov, go test -cover, ecc.)

2. Se non è configurato, suggerisci l'installazione e chiedi conferma.

3. Esegui il report di coverage.

4. Analizza i risultati e presenta:
   ```
   ## Coverage Report

   | File/Modulo | Linee | Branch | Funzioni |
   |-------------|-------|--------|----------|
   | [file]      | XX%   | XX%    | XX%      |

   ## Zone non coperte critiche
   - [file:righe] — [perché è critico]

   ## Suggerimento
   Esegui `/tdd red` per aggiungere test per le zone scoperte.
   ```

---

## Regole Globali

### Cosa questa skill NON fa mai
- **NON** scrive codice di produzione senza test che falliscono
- **NON** modifica test per farli passare (a meno che siano genuinamente sbagliati)
- **NON** aggiunge funzionalità non richieste "perché tanto ci siamo"
- **NON** skippa la fase di refactoring

### Adattamento al linguaggio
La skill rileva automaticamente linguaggio e framework dal progetto e adatta:
- Sintassi dei test
- Import e convenzioni di naming
- Sistema di mock/stub nativo
- Struttura directory dei test
- Comandi di esecuzione test

### Interazione con altri workflow
- Se esiste `phases/` (skill phase-plan), integra i test nei deliverable della fase corrente
- Se esiste `CLAUDE.md` con sezione Testing, rispetta le convenzioni definite
- Se il progetto ha CI/CD, verifica che i test siano compatibili con la pipeline

