---
name: code-review
description: "Review semantica del codice: analizza naming, responsabilità, coerenza con le convenzioni del progetto, edge case non gestiti, accoppiamento. Genera report con severity e fix proposti."
user-invocable: true
argument-hint: "[file|directory|fase N]"
---

# Code Review — Review Semantica

Analizza il codice per problemi che un linter non trova: responsabilità miste, naming debole, accoppiamento nascosto, edge case non gestiti, incoerenza con le convenzioni. Non duplica linter o tool statici.

**Argomenti:** `<file>` | `<directory>` | `fase <N>`

---

## Flusso

### 1. Determina scope

- **`<file>`** — singolo file
- **`<directory>`** — tutti i file sorgente (escludi test, config, asset)
- **`fase <N>`** — leggi `phases/STATE.md`, fai review dei file prodotti nella fase
- **Nessun argomento** — chiedi all'utente

### 2. Carica contesto

1. Se esiste `phases/CONTRACTS.md` → leggilo per convenzioni
2. Se esiste `CLAUDE.md` → leggilo per regole e pattern
3. Se nessuno esiste → inferisci dal codice circostante

### 3. Analizza (7 dimensioni)

**Responsabilità/Struttura:** SRP, funzioni >50 righe, troppi parametri, God Object/Function

**Naming:** nomi generici (data, temp, result), coerenza con progetto, abbreviazioni ambigue

**Gestione errori:** catch vuoti, messaggi utili, eccezioni specifiche, test per casi errore

**Accoppiamento:** DI vs hardcoded, dipendenze circolari, violazione Law of Demeter, accesso dati isolato

**Edge case:** null/vuoti, race condition, collection vuote, limiti numerici, assunzioni implicite

**Coerenza progetto:** rispetto CONTRACTS.md, struttura cartelle, pattern coerenti, tipi condivisi

**Sicurezza (se rilevante):** input validato, SQL parametrizzato, no secrets hardcoded, dati sensibili non esposti

### 4. Report

Raggruppa per severity:
- **Critico** — bug, vulnerabilità, violazione convenzioni. Include FILE:RIGA, problema, fix
- **Importante** — design che degrada manutenibilità. Include problema e suggerimento
- **Suggerimento** — stile, non bloccante
- **Positivo** — cose fatte bene
- **Riepilogo** — tabella valutazione per dimensione (buono/da migliorare/critico)

### 5. Chiedi come procedere

- Applicare fix critici?
- Applicare tutti i fix?
- Solo informativo?

---

## Review post-fase

Quando invocato con `fase <N>`:
1. Leggi la fase e CONTRACTS.md
2. Review standard sui file prodotti
3. Verifica in più: deliverable presenti, codice rispetta contratti, stato del mondo all'uscita corrisponde alla realtà

---

## Regole

- NON segnala problemi di formattazione (lavoro del linter)
- NON esegue codice o test
- NON riscrive codice senza conferma
- Sii specifico e proponi il fix concreto
- Rispetta il contesto (prototipo ≠ produzione)
