---
name: phase-plan
description: Decompone PROJECT.md in fasi atomiche e session-proof con sistema a tre livelli (CONTRACTS + STATE + fasi). Integra TDD nel ciclo di esecuzione. Usa /phase-plan init per generare, /phase-plan exec N per eseguire una fase.
user-invocable: true
argument-hint: "[init | exec N | status | complete N]"
---

# Phase Plan

Decompone un file PROJECT.md in fasi di sviluppo atomiche e session-proof, con un sistema a tre livelli che garantisce coerenza cross-fase minimizzando il consumo di token.

**Ogni fase viene eseguita con workflow TDD integrato: Red → Green → Refactor.**

**Argomenti:** `init` | `exec <N>` | `status` | `complete <N>`

---

## Modalità: `init`

Genera la struttura a tre livelli a partire da PROJECT.md.

### Istruzioni

1. **Cerca `PROJECT.md`** nella root del progetto. Se non esiste, avvisa l'utente e suggerisci di crearlo usando il template disponibile in `~/.claude/skills/project_template.md`.

2. **Leggi e analizza `PROJECT.md`** interamente. Estrai:
   - Stack tecnologico e versioni
   - Entità del dominio, campi e relazioni
   - Funzionalità raggruppate per area
   - Vincoli architetturali e decisioni
   - Requisiti non funzionali

3. **Rileva il contesto di testing del progetto:**
   - Test runner in uso o da configurare (Jest, PHPUnit, pytest, Go test, Vitest, ecc.)
   - Struttura test esistente (directory, naming convention)
   - Pattern di mock/stub già usati
   - Se non esiste un test runner, includi la sua configurazione come prerequisito nella prima fase

4. **Progetta la decomposizione in fasi.** Ogni fase deve essere:
   - **Atomica** — completabile in una singola sessione di lavoro
   - **Autosufficiente** — contiene tutto ciò che serve per essere eseguita (insieme a CONTRACTS.md e STATE.md)
   - **Verificabile** — ha criteri di completamento chiari e testabili
   - **Ordinata** — dichiara esplicitamente le dipendenze da altre fasi
   - **Test-first** — ogni task include i comportamenti attesi che diventeranno test

5. **Presenta all'utente** la decomposizione proposta prima di scrivere qualsiasi file:
   - Elenco delle fasi con nome e descrizione breve
   - Grafo delle dipendenze (quale fase dipende da quale)
   - Contenuto proposto per CONTRACTS.md (sintesi)
   - Convenzioni di testing scelte (runner, struttura, naming)
   - Chiedi conferma o modifiche

6. **Solo dopo approvazione**, crea la directory `phases/` e genera i file.

### File da generare

#### `phases/CONTRACTS.md`

Massimo ~150 righe. Contiene SOLO ciò che deve restare coerente tra le fasi.

```markdown
# Contratti di Progetto

## Changelog
| Versione | Data | Modifica |
| -------- | ---- | -------- |
| 1.0 | [data] | Creazione iniziale |

## Convenzioni Naming
<!-- Pattern di naming per file, componenti, variabili, route, tabelle DB -->

## Struttura Cartelle
<!-- Albero delle directory principali del progetto -->

## Interfacce e Tipi Condivisi
<!-- Tipi/interfacce usati da più di una fase. Scrivili in sintassi del linguaggio scelto. -->

## Schema Dati
<!-- Entità, campi chiave e relazioni. Non duplicare tutto PROJECT.md:
     includi solo ciò che serve per la coerenza cross-fase. -->

## Convenzioni API
<!-- Pattern per endpoint, formato risposte, gestione errori, autenticazione -->

## Convenzioni Testing
<!-- Test runner, struttura directory test, naming pattern dei test,
     pattern mock/stub standard, comandi di esecuzione -->

## Dipendenze e Versioni
<!-- Lista delle dipendenze principali con versione esatta -->
```

#### `phases/STATE.md`

```markdown
# Stato del Progetto

## Riepilogo
- **Fase corrente:** Nessuna (progetto appena inizializzato)
- **Fasi completate:** 0 / [totale]
- **Ultima modifica:** [data]

## Fasi

| # | Nome | Stato | Test | Data completamento | Note |
|---|------|-------|------|--------------------|------|
| 01 | [nome] | pending | - | - | - |
| 02 | [nome] | pending | - | - | - |

## Deviazioni dai Contratti
<!-- Nessuna deviazione registrata -->

## Changelog Contratti
<!-- Nessuna modifica registrata -->
```

#### `phases/NN-nome-fase.md` (uno per ogni fase)

```markdown
# Fase NN: [Nome Fase]

## Metadata
- **ID:** NN
- **Priorità:** [alta/media/bassa]
- **Dipendenze:** [lista ID fasi prerequisito, o "nessuna"]
- **Produce per:** [lista ID fasi che dipendono da questa]
- **Sezioni CONTRACTS.md rilevanti:** [lista sezioni da consultare]

## Stato del Mondo all'Ingresso
<!-- Cosa esiste GIÀ nel codice quando questa fase inizia.
     Descrivi solo ciò che questa fase tocca/usa. Sii specifico:
     file che esistono, endpoint disponibili, tabelle create, ecc. -->

## Task

### Task 1: [Nome]
<!-- Descrizione dettagliata di cosa fare, con contesto sufficiente
     per eseguirlo senza leggere altre fasi o PROJECT.md -->

**Comportamenti attesi (diventeranno test):**
- Dato [input/stato], quando [azione], allora [risultato atteso]
- Dato [input invalido], quando [azione], allora [errore atteso]
- [Edge case]: [comportamento]

### Task 2: [Nome]
<!-- ... -->

**Comportamenti attesi (diventeranno test):**
- ...

## Deliverable e Verifiche
<!-- Cosa deve esistere alla fine di questa fase.
     Criteri verificabili: file creati, test che passano,
     endpoint che rispondono, ecc. -->

- [ ] [Deliverable 1] — [criterio di verifica]
- [ ] [Deliverable 2] — [criterio di verifica]
- [ ] Tutti i test della fase passano
- [ ] Coverage minima rispettata per il codice della fase

## Stato del Mondo all'Uscita
<!-- Cosa esiste nel codice DOPO che questa fase è completata.
     Questo diventa l'"Ingresso" per le fasi successive. -->
```

---

## Modalità: `exec <N>`

Esegue la fase N del progetto con **workflow TDD integrato**.

### Istruzioni

1. **Leggi SOLO questi 3 file** (non leggere PROJECT.md né altre fasi):
   - `phases/CONTRACTS.md`
   - `phases/STATE.md`
   - `phases/NN-*.md` (il file della fase richiesta)

2. **Valida i prerequisiti:**
   - Controlla in STATE.md che tutte le fasi in "Dipendenze" siano marcate come `completed`
   - Se una dipendenza non è completata, avvisa l'utente e chiedi come procedere

3. **Verifica lo stato del mondo all'ingresso:**
   - Controlla che gli artefatti dichiarati nella sezione "Stato del Mondo all'Ingresso" esistano effettivamente nel codice (file, cartelle, endpoint, tabelle, ecc.)
   - Se qualcosa manca, segnala la discrepanza all'utente prima di procedere

4. **Per ogni task della fase, esegui il ciclo TDD:**

   #### Passo 4a — RED: Genera i test

   - Leggi la sezione "Comportamenti attesi" del task
   - Genera i test seguendo le convenzioni in CONTRACTS.md (sezione "Convenzioni Testing"):
     - **Unit test** per ogni comportamento atteso (happy path, edge case, errori)
     - **Integration test** se il task coinvolge interazione tra componenti
   - Ogni test segue il pattern AAA (Arrange-Act-Assert)
   - Naming: `test_[unità]_[scenario]_[risultato_atteso]`
   - **Esegui i test e verifica che falliscano tutti**
   - Se un test passa senza codice di produzione, il test è sbagliato — correggilo
   - Comunica all'utente: `🔴 RED: X test generati, tutti falliscono correttamente`

   #### Passo 4b — GREEN: Implementa il codice minimo

   - Scrivi **solo** il codice necessario per far passare i test
   - **NON** aggiungere funzionalità extra, gestione errori non testata, o ottimizzazioni
   - **NON** implementare casi che non hanno un test corrispondente
   - Preferisci l'implementazione più semplice e diretta possibile
   - Esegui i test dopo ogni modifica significativa
   - Mai modificare un test per farlo passare (a meno che il test sia genuinamente sbagliato)
   - Comunica all'utente: `🟢 GREEN: X/X test passano`

   #### Passo 4c — REFACTOR: Migliora il codice

   - Analizza il codice appena scritto cercando: duplicazione, nomi poco espressivi, funzioni troppo lunghe, responsabilità multiple
   - Analizza i test cercando: setup ripetuto estraibile, assert poco chiari, test fragili
   - Applica miglioramenti in piccoli passi, eseguendo i test dopo ogni modifica
   - Se un test fallisce durante il refactoring, annulla l'ultimo cambiamento
   - Comunica all'utente: `🔵 REFACTOR: completato, X/X test ancora verdi`

   #### Poi passa al task successivo della fase.

5. **Durante l'esecuzione**, se scopri che serve una modifica ai contratti (nuovo tipo condiviso, campo aggiuntivo nel DB, cambio di convenzione):
   - **NON** apportare la modifica silenziosamente
   - Segnala all'utente la necessità di modifica a CONTRACTS.md
   - Procedi solo dopo approvazione

6. **Al termine di tutti i task**, verifica i deliverable elencati nella fase (incluso "Tutti i test della fase passano") e segnala all'utente lo stato di completamento.

---

## Modalità: `status`

Mostra lo stato sintetico del progetto.

### Istruzioni

1. Leggi `phases/STATE.md`
2. Presenta un riepilogo:
   - Fasi completate vs totali
   - Fase corrente o prossima fase eseguibile
   - Stato test per ogni fase completata (colonna Test)
   - Eventuali deviazioni attive dai contratti
   - Eventuali fasi bloccate e perché

---

## Modalità: `complete <N>`

Segna una fase come completata e aggiorna lo stato.

### Istruzioni

1. Leggi `phases/STATE.md` e `phases/NN-*.md`

2. **Verifica completamento:**
   - Controlla che tutti i deliverable della fase siano soddisfatti
   - **Esegui tutti i test della fase** e verifica che passino
   - Se qualcosa non è soddisfatto, segnala e chiedi conferma

3. **Aggiorna `phases/STATE.md`:**
   - Cambia lo stato della fase da `in_progress` a `completed`
   - Aggiorna la colonna Test con il conteggio (es. `12/12 ✓`)
   - Aggiungi data di completamento
   - Aggiungi note su eventuali deviazioni o decisioni prese durante l'esecuzione
   - Se ci sono state modifiche a CONTRACTS.md, registrale nel "Changelog Contratti"

4. **Aggiorna `phases/CONTRACTS.md`** se ci sono modifiche approvate dall'utente durante l'esecuzione:
   - Aggiungi una riga al Changelog con versione, data e descrizione della modifica
   - Applica la modifica nella sezione appropriata

5. **Mostra la prossima fase eseguibile** (quella con tutte le dipendenze soddisfatte).
