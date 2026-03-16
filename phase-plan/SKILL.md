---
name: phase-plan
description: Decompone PROJECT.md in fasi atomiche e session-proof con sistema a tre livelli (CONTRACTS + STATE + fasi). Integra TDD nel ciclo di esecuzione. Usa /phase-plan init per generare, /phase-plan exec N per eseguire una fase.
user-invocable: true
argument-hint: "[init | exec N | status | complete N]"
---

# Phase Plan

Decompone PROJECT.md in fasi atomiche e session-proof con sistema a tre livelli che garantisce coerenza cross-fase. Ogni fase viene eseguita con workflow TDD integrato: Red → Green → Refactor.

**Argomenti:** `init` | `exec <N>` | `status` | `complete <N>`

---

## `init`

Genera la struttura a tre livelli da PROJECT.md.

1. **Cerca `PROJECT.md`** nella root. Se manca, suggerisci il template da `~/.claude/skills/project_template.md`.

2. **Analizza PROJECT.md:** stack, entità/relazioni, funzionalità per area, vincoli architetturali, requisiti non funzionali.

3. **Rileva contesto testing:** runner, struttura test, pattern mock. Se manca il runner, includi configurazione come prerequisito nella prima fase.

4. **Progetta la decomposizione.** Ogni fase deve essere:
   - **Atomica** — completabile in una sessione
   - **Autosufficiente** — tutto il necessario è in CONTRACTS.md + STATE.md + file fase
   - **Verificabile** — criteri di completamento chiari e testabili
   - **Ordinata** — dipendenze esplicite
   - **Test-first** — ogni task include comportamenti attesi

5. **Presenta all'utente** la decomposizione proposta: elenco fasi, grafo dipendenze, sintesi CONTRACTS.md, convenzioni testing. Chiedi conferma.

6. **Solo dopo approvazione**, crea `phases/` e genera i file:

### `phases/CONTRACTS.md` (~150 righe max)
Contiene SOLO ciò che deve restare coerente tra fasi: changelog, convenzioni naming, struttura cartelle, interfacce/tipi condivisi, schema dati essenziale, convenzioni API, convenzioni testing, dipendenze con versioni.

### `phases/STATE.md`
Riepilogo progetto: fase corrente, fasi completate/totali, ultima modifica. Tabella fasi (ID, nome, stato, test, data completamento, note). Sezioni per deviazioni dai contratti e changelog contratti.

### `phases/NN-nome-fase.md` (uno per fase)
Contiene: metadata (ID, priorità, dipendenze, produce-per, sezioni CONTRACTS rilevanti), stato del mondo all'ingresso, task con comportamenti attesi (che diventeranno test in formato "Dato X, quando Y, allora Z"), deliverable con verifiche, stato del mondo all'uscita.

---

## `exec <N>`

Esegue la fase N con workflow TDD integrato.

1. **Leggi SOLO 3 file** (non PROJECT.md né altre fasi): `phases/CONTRACTS.md`, `phases/STATE.md`, `phases/NN-*.md`

2. **Valida prerequisiti:** in STATE.md tutte le dipendenze devono essere `completed`. Se no, avvisa.

3. **Verifica stato del mondo all'ingresso:** gli artefatti dichiarati devono esistere. Se manca qualcosa, segnala prima di procedere.

4. **Per ogni task, ciclo TDD:**

   **RED:** Genera test dai "Comportamenti attesi" (unit + integration se serve). Pattern AAA, naming `test_[unità]_[scenario]_[risultato]`. Esegui e verifica che falliscano tutti. Se un test passa senza codice, è sbagliato. Comunica: `🔴 RED: X test, tutti falliscono`

   **GREEN:** Scrivi solo il codice per far passare i test. Niente extra, niente feature non testate. Mai modificare test per farli passare. Comunica: `🟢 GREEN: X/X passano`

   **REFACTOR:** Analizza codice e test per duplicazione, nomi, funzioni lunghe. Migliora in piccoli passi con test dopo ogni modifica. Se fallisce, annulla. Comunica: `🔵 REFACTOR: completato, X/X verdi`

5. **Se serve modifica ai contratti** (nuovo tipo, campo DB, cambio convenzione): NON farla silenziosamente. Segnala e procedi solo dopo approvazione.

6. **Al termine**, verifica tutti i deliverable e segnala stato completamento.

---

## `status`

1. Leggi `phases/STATE.md`
2. Mostra: fasi completate/totali, fase corrente o prossima eseguibile, stato test per fase, deviazioni attive, fasi bloccate

---

## `complete <N>`

1. Leggi `phases/STATE.md` e `phases/NN-*.md`
2. Verifica: tutti i deliverable soddisfatti, tutti i test passano
3. Aggiorna STATE.md: stato → completed, colonna test con conteggio, data, note deviazioni
4. Se ci sono modifiche approvate a CONTRACTS.md, applicale e registra nel changelog
5. Mostra la prossima fase eseguibile
