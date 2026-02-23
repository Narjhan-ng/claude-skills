---
name: phase-plan
description: Decompone PROJECT.md in fasi atomiche e session-proof con sistema a tre livelli (CONTRACTS + STATE + fasi). Usa /phase-plan init per generare, /phase-plan exec N per eseguire una fase.
user-invocable: true
argument-hint: "[init | exec N | status | complete N]"
---

# Phase Plan

Decompone un file PROJECT.md in fasi di sviluppo atomiche e session-proof, con un sistema a tre livelli che garantisce coerenza cross-fase minimizzando il consumo di token.

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

3. **Progetta la decomposizione in fasi.** Ogni fase deve essere:
   - **Atomica** — completabile in una singola sessione di lavoro
   - **Autosufficiente** — contiene tutto ciò che serve per essere eseguita (insieme a CONTRACTS.md e STATE.md)
   - **Verificabile** — ha criteri di completamento chiari e testabili
   - **Ordinata** — dichiara esplicitamente le dipendenze da altre fasi

4. **Presenta all'utente** la decomposizione proposta prima di scrivere qualsiasi file:
   - Elenco delle fasi con nome e descrizione breve
   - Grafo delle dipendenze (quale fase dipende da quale)
   - Contenuto proposto per CONTRACTS.md (sintesi)
   - Chiedi conferma o modifiche

5. **Solo dopo approvazione**, crea la directory `phases/` e genera i file.

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

| # | Nome | Stato | Data completamento | Note |
|---|------|-------|--------------------|------|
| 01 | [nome] | pending | - | - |
| 02 | [nome] | pending | - | - |

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

### Task 2: [Nome]
<!-- ... -->

## Deliverable e Verifiche
<!-- Cosa deve esistere alla fine di questa fase.
     Criteri verificabili: file creati, test che passano,
     endpoint che rispondono, ecc. -->

- [ ] [Deliverable 1] — [criterio di verifica]
- [ ] [Deliverable 2] — [criterio di verifica]

## Stato del Mondo all'Uscita
<!-- Cosa esiste nel codice DOPO che questa fase è completata.
     Questo diventa l'"Ingresso" per le fasi successive. -->
```

---

## Modalità: `exec <N>`

Esegue la fase N del progetto.

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

4. **Esegui i task** della fase in ordine, rispettando i contratti in CONTRACTS.md.

5. **Durante l'esecuzione**, se scopri che serve una modifica ai contratti (nuovo tipo condiviso, campo aggiuntivo nel DB, cambio di convenzione):
   - **NON** apportare la modifica silenziosamente
   - Segnala all'utente la necessità di modifica a CONTRACTS.md
   - Procedi solo dopo approvazione

6. **Al termine**, verifica i deliverable elencati nella fase e segnala all'utente lo stato di completamento.

---

## Modalità: `status`

Mostra lo stato sintetico del progetto.

### Istruzioni

1. Leggi `phases/STATE.md`
2. Presenta un riepilogo:
   - Fasi completate vs totali
   - Fase corrente o prossima fase eseguibile
   - Eventuali deviazioni attive dai contratti
   - Eventuali fasi bloccate e perché

---

## Modalità: `complete <N>`

Segna una fase come completata e aggiorna lo stato.

### Istruzioni

1. Leggi `phases/STATE.md` e `phases/NN-*.md`

2. **Verifica completamento:** controlla che tutti i deliverable della fase siano soddisfatti. Se qualcuno non lo è, segnala e chiedi conferma.

3. **Aggiorna `phases/STATE.md`:**
   - Cambia lo stato della fase da `in_progress` a `completed`
   - Aggiungi data di completamento
   - Aggiungi note su eventuali deviazioni o decisioni prese durante l'esecuzione
   - Se ci sono state modifiche a CONTRACTS.md, registrale nel "Changelog Contratti"

4. **Aggiorna `phases/CONTRACTS.md`** se ci sono modifiche approvate dall'utente durante l'esecuzione:
   - Aggiungi una riga al Changelog con versione, data e descrizione della modifica
   - Applica la modifica nella sezione appropriata

5. **Mostra la prossima fase eseguibile** (quella con tutte le dipendenze soddisfatte).
