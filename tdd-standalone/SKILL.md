---
name: tdd
description: "Workflow TDD standalone: genera test dai requisiti PRIMA del codice, poi guida Red→Green→Refactor. Usa per feature isolate o codice esistente senza phase-plan."
user-invocable: true
argument-hint: "[feature <descrizione> | red | green | refactor | status | coverage]"
---

# TDD — Test-Driven Development

Workflow TDD autentico: test prima, codice minimo, poi migliora. Per qualsiasi linguaggio e framework.

**Uso standalone** per progetti senza phase-plan o feature isolate. Se il progetto usa `/phase-plan`, il TDD è già integrato in `exec N`.

**Argomenti:** `feature <descrizione>` | `red` | `green` | `refactor` | `status` | `coverage`

---

## Principi

1. Mai codice di produzione senza un test che fallisce
2. Il test definisce il comportamento atteso, non il codice esistente
3. Codice minimo per far passare il test
4. Refactor solo a test verdi
5. I test sono documentazione vivente

---

## `feature <descrizione>`

1. **Rileva contesto progetto:** linguaggio, test runner, struttura test, pattern mock esistenti. Se manca test runner, suggerisci quello standard e chiedi conferma.

2. **Analizza requisiti.** Dalla descrizione estrai: happy path, edge case, casi errore, interazioni con dipendenze, effetti collaterali.

3. **Presenta piano test** prima di scrivere codice. Raggruppa in Unit (70%) / Integration (20%) / E2E (10%). Includi mock e fixture necessari. Chiedi conferma.

4. **Dopo approvazione, genera i test** seguendo:
   - Pattern AAA (Arrange-Act-Assert)
   - Naming: `test_[unità]_[scenario]_[risultato_atteso]`
   - Ordine: unit → integration → E2E
   - Mock l'interfaccia, non l'implementazione
   - Fixture in file dedicati se riusate, ogni test indipendente

5. **Esegui test, verifica che falliscano tutti.** Se un test passa senza codice di produzione, è sbagliato. Comunica: `🔴 RED: X test generati, tutti falliscono`

6. **Aggiorna `TDD_STATUS.md`** con feature, fase RED, tabella test, prossimo passo.

---

## `red`

Aggiunge nuovi test per comportamenti non coperti.

1. Leggi `TDD_STATUS.md`
2. Chiedi quale comportamento testare
3. Genera test con le stesse regole di `feature`
4. Esegui e verifica che i nuovi falliscano
5. Aggiorna `TDD_STATUS.md`

---

## `green`

Implementa il codice **minimo** per far passare i test rossi.

1. Leggi `TDD_STATUS.md` e i file di test
2. Scrivi **solo** il codice per far passare i test — niente extra, niente feature non testate, implementazione più semplice possibile
3. Esegui test dopo ogni modifica. Mai modificare un test per farlo passare (salvo test genuinamente sbagliato)
4. Quando tutti passano: `🟢 GREEN: X/X test passano`. Aggiorna `TDD_STATUS.md`
5. Se un test è impossibile da far passare senza cambiare design, segnala e proponi alternative

---

## `refactor`

Migliora codice e test mantenendo tutti i test verdi. Disponibile solo se stato = GREEN.

1. Analizza codice: duplicazione, nomi, funzioni lunghe, responsabilità multiple
2. Analizza test: setup ripetuto, assert poco chiari, test fragili
3. Presenta piano refactoring, chiedi conferma
4. Applica in piccoli passi, test dopo ogni modifica. Se fallisce, annulla
5. `🔵 REFACTOR: completato, X/X test verdi`. Aggiorna `TDD_STATUS.md`

---

## `status`

Leggi `TDD_STATUS.md`. Mostra: feature, fase corrente, conteggio test, prossima azione.

---

## `coverage`

1. Identifica tool di coverage del progetto
2. Se manca, suggerisci installazione
3. Esegui report e presenta tabella per file con linee/branch/funzioni
4. Evidenzia zone scoperte critiche, suggerisci `/tdd red`

---

## Regole

- NON scrive codice senza test che falliscono
- NON modifica test per farli passare
- NON aggiunge funzionalità non richieste
- Se esiste `phases/`, integra nei deliverable della fase
- Se esiste `CLAUDE.md` con sezione Testing, rispetta le convenzioni
