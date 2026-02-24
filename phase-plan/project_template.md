# Project: [Nome del Progetto]

> Questo template serve come guida per scrivere il file PROJECT.md del tuo progetto.
> Compila ogni sezione sostituendo i placeholder tra parentesi quadre.
> Rimuovi questa nota e i commenti `<!-- -->` una volta completato.

---

## Overview

<!-- Una descrizione concisa: cosa fa il progetto, per chi è, quale problema risolve. 3-5 frasi. -->

[Descrizione del progetto]

## Tech Stack

| Layer        | Tecnologia    | Versione | Note                  |
| ------------ | ------------- | -------- | --------------------- |
| Frontend     | [es. React]   | [x.y]    | [eventuali dettagli]  |
| Backend      | [es. Node.js] | [x.y]    |                       |
| Database     | [es. PostgreSQL] | [x.y] |                       |
| ORM/Query    | [es. Prisma]  | [x.y]    |                       |
| Auth         | [es. JWT]     |          |                       |
| Infra/Deploy | [es. Docker]  |          |                       |
| Altro        |               |          |                       |

## Entità Principali

<!-- Descrivi i modelli/entità del dominio con i campi principali e le relazioni.
     Non serve essere esaustivi: includi ciò che è necessario per comprendere il dominio. -->

### [Entità 1]

| Campo    | Tipo     | Descrizione         |
| -------- | -------- | ------------------- |
| id       | UUID     | Identificativo unico |
| [campo]  | [tipo]   | [descrizione]       |

**Relazioni:** [es. "Ha molti Order", "Appartiene a Organization"]

### [Entità 2]

| Campo    | Tipo     | Descrizione         |
| -------- | -------- | ------------------- |
| [campo]  | [tipo]   | [descrizione]       |

**Relazioni:** [...]

<!-- Aggiungi altre entità secondo necessità -->

## Funzionalità

<!-- Raggruppa le funzionalità per area logica. Usa checkbox per tracciare lo stato. -->

### Area: [Nome Area 1]

- [ ] **[Nome funzionalità]** — [Descrizione di cosa deve fare, comportamento atteso, eventuali edge case]
- [ ] **[Nome funzionalità]** — [Descrizione]

### Area: [Nome Area 2]

- [ ] **[Nome funzionalità]** — [Descrizione]
- [ ] **[Nome funzionalità]** — [Descrizione]

<!-- Aggiungi altre aree secondo necessità -->

## Requisiti Non Funzionali

- **Performance:** [es. "Tempo di risposta API < 200ms per il 95° percentile"]
- **Sicurezza:** [es. "Autenticazione obbligatoria per tutte le route tranne /login"]
- **Accessibilità:** [es. "WCAG 2.1 AA"]
- **Scalabilità:** [es. "Supporto fino a 10k utenti concorrenti"]
- **Testing:** [es. "Copertura minima 80% su business logic"]
- **Altro:** [...]

## Vincoli e Decisioni Architetturali

<!-- Decisioni già prese che NON devono essere ridiscusse durante l'implementazione.
     Librerie obbligatorie, pattern da seguire, vincoli esterni, integrazioni fisse. -->

- [es. "Usare il pattern Repository per l'accesso dati"]
- [es. "Tutte le API devono seguire le convenzioni REST"]
- [es. "Il frontend deve usare Server Components dove possibile"]

## Note Aggiuntive

<!-- Qualsiasi informazione utile che non rientra nelle sezioni precedenti:
     context di business, deadline, priorità, riferimenti esterni, mockup, ecc. -->

[Note libere]
