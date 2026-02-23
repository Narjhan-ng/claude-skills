name: ui-ux-check
description: |
  Analizza e corregge problemi di UI/UX in componenti esistenti.
  Invoca con: /ui-ux-check [file|directory|componente]
  Verifica: design tokens, component patterns, accessibilità, terminologia italiana.
argument-hint: "[file/directory/component-name]"
allowed-tools: Read, Grep, Glob, Edit, Write
model: sonnet
disable-model-invocation: false
---

# UI/UX Consistency Skill

Analizza e corregge problemi di coerenza UI/UX nel codice esistente.

## Come Usare

Invoca manualmente con:
```
/ui-ux-check                     # Analizza tutto il progetto
/ui-ux-check src/components      # Analizza una directory
/ui-ux-check LoginForm.tsx       # Analizza un file specifico
/ui-ux-check UserCard            # Cerca e analizza un componente
```

---

## Processo di Esecuzione

### Step 1: Caricamento Guidelines

**Prima azione:**
1. Leggi `.claude/skills/ui-ux-check/ui-ux-guidelines.yaml`
2. Carica in memoria:
   - Design tokens (colors, spacing, typography)
   - Component patterns (button, input, card, modal)
   - Interaction patterns (loading, error handling, validation)
   - Terminology (IT → italiano)
   - Accessibility rules
   - Naming conventions
   - Forbidden patterns
3. Mostra conferma caricamento

---

### Step 2: Identificazione File

Se `$ARGUMENTS` specificato:
- Se è un **file**: analizza quel file
- Se è una **directory**: trova tutti i file UI (`**/*.tsx`, `**/*.jsx`, `**/*.vue`, `**/*.css`, etc.)
- Se è un **nome componente**: cerca con Grep il componente nel progetto

Se **NON** specificato:
- Trova tutti i file UI nel progetto:
  - `**/*.tsx`, `**/*.jsx`, `**/*.vue`, `**/*.svelte`
  - `**/*.css`, `**/*.scss`, `**/*.module.css`

---

### Step 3: Analisi e Verifica

Per ogni file/componente, verifica le seguenti categorie:

#### A. Design Tokens

**Colori:**
- ❌ **Violation**: Colori hardcoded (`#FF0000`, `rgb(...)`, colori in inglese come `red`, `blue`)
- ✅ **Fix**: Usa design token
  - `color: #003366` → `color: var(--color-primary)`
  - `background: #DC3545` → `background: var(--color-error)`
  - `border-color: red` → `border-color: var(--color-error)`

**Spacing:**
- ❌ **Violation**: Padding/margin con valori non nella scale (es: `15px`, `18px`)
- ✅ **Fix**: Usa multipli di 8px dalla scale
  - `padding: 15px` → `padding: 16px` o `var(--spacing-md)`
  - `margin: 25px` → `margin: 24px` o `var(--spacing-lg)`

**Typography:**
- ❌ **Violation**: Font-family non definito nel design system
- ✅ **Fix**: Usa font definiti
  - `font-family: Arial` → `font-family: var(--font-primary)`
  - `font-size: 17px` → `font-size: var(--font-lg)` o `18px`

#### B. Componenti Visivi

**Button:**
- Verifica: Classe base `btn` presente
- Verifica: Varianti corrette (`btn-primary`, `btn-secondary`, `btn-outline`, `btn-text`)
- Verifica: Props obbligatorie: `onClick`, `aria-label`
- Verifica: Stati implementati: `hover`, `active`, `disabled`, `loading`

**Input:**
- Verifica: Classe base `input` presente
- Verifica: Props obbligatorie: `id`, `name`, `label`, `aria-describedby`
- Verifica: Stati: `focus`, `error`, `disabled`
- Verifica: Validazione implementata

**Card:**
- Verifica: Struttura corretta: `card-header`, `card-body`, `card-footer`
- Verifica: Spacing interno: `16px`

**Modal:**
- Verifica: Features obbligatorie: close button, overlay, escape key handler, focus trap
- Verifica: Accessibilità implementata

#### C. Pattern di Interazione

**Loading States:**
- Verifica: Operazioni async hanno feedback loading
- Fix: Aggiungi `LoadingSpinner` per operazioni async
- Fix: Aggiungi `SkeletonLoader` per initial data load
- Fix: Aggiungi `ProgressBar` per operazioni multi-step

**Error Handling:**
- Verifica: Errori mostrati all'utente
- Fix: Aggiungi `Toast` per errori generali (position: top-right, duration: 4000ms)
- Fix: Aggiungi `ErrorMessage` inline per validazione form
- Fix: Implementa `ErrorBoundary` per errori di componenti

**Form Validation:**
- Verifica: Validazione su `onBlur`
- Verifica: Errori mostrati inline sotto il field
- Verifica: Submit button disabilitato quando ci sono errori
- Verifica: Success feedback presente

#### D. Terminologia

**Cerca e sostituisci termini inglesi nell'UI con italiano:**

| ❌ Trovato | ✅ Sostituisci |
|-----------|---------------|
| Customer | Cliente |
| Wholesaler | Wholesaler (o WS) |
| Provider | Fornitore |
| Admin | Admin (o A) |
| Invoice | Fattura |
| Ticket | Ticket |
| Request | Richiesta |
| Status | Stato |
| Create | Crea |
| Edit | Modifica |
| Delete | Elimina |
| Save | Salva |
| Cancel | Annulla |
| Confirm | Conferma |
| Send | Invia |
| Download | Scarica |
| Pending | In attesa |
| In Progress | In elaborazione |
| Completed | Completato |
| Failed | Fallito |
| Cancelled | Annullato |

**Verifica consistenza:**
- Stesso termine usato ovunque (es: sempre "Cliente", mai "Utente" o "Customer")

#### E. Accessibilità

- [ ] **Semantic HTML**: Usa `<button>`, `<nav>`, `<header>`, `<main>`, `<article>`, etc.
- [ ] **ARIA Labels**: Tutti gli elementi interattivi hanno `aria-label` o `aria-labelledby`
- [ ] **Keyboard Navigation**: Tab order logico, focus visibile
- [ ] **Focus States**: `:focus-visible` implementato
- [ ] **Color Contrast**: Contrasto minimo 4.5:1 (WCAG AA)
- [ ] **Alt Text**: Tutte le immagini hanno `alt` descrittivo

#### F. Naming Conventions

- ❌ **Violation**: Naming non conforme
- ✅ **Fix**: Applica convenzioni
  - Componenti: `PascalCase` (es: `UserProfile`)
  - File: `kebab-case` (es: `user-profile.tsx`)
  - Classi CSS: `kebab-case` (es: `user-profile-card`)
  - Funzioni: `camelCase` (es: `handleSubmit`)
  - Costanti: `UPPER_SNAKE_CASE` (es: `API_ENDPOINT`)

#### G. Forbidden Patterns

- ❌ **Inline styles**: Usa classi CSS
  - `<div style={{color: 'red'}}>` → `<div className="text-error">`
- ❌ **Hardcoded colors**: Usa design tokens
- ❌ **Magic numbers**: Usa costanti denominate
  - `setTimeout(() => {}, 5000)` → `setTimeout(() => {}, TOAST_DURATION)`
- ❌ **Inconsistent spacing**: Usa scale definita
- ❌ **English labels in UI**: Usa italiano

---

### Step 4: Generazione Report

Crea report strutturato con severity levels:

```markdown
# UI/UX Consistency Check Report

📅 Data: YYYY-MM-DD HH:mm
📁 File analizzati: X

## Summary
- 🔴 Critical: X problemi
- 🟡 Warning: X problemi
- 🔵 Info: X suggerimenti

---

## Problemi Trovati

### 🔴 Critical

#### [src/components/Button.tsx:15]
**Issue**: Colore hardcoded non nei design tokens
**Found**: `color: #003366`
**Should be**: `color: var(--color-primary)`
**Category**: Design Tokens > Colors
**Auto-fix**: ✅ Applicato

---

### 🟡 Warning

#### [src/components/LoginForm.tsx:42]
**Issue**: Terminologia inglese nell'UI
**Found**: `<button>Save</button>`
**Should be**: `<button>Salva</button>`
**Category**: Terminology
**Auto-fix**: ✅ Applicato

---

### 🔵 Info

#### [src/components/UserCard.tsx:28]
**Issue**: Spacing non ottimale
**Found**: `padding: 15px`
**Suggested**: `padding: 16px` (multiplo di 8)
**Category**: Design Tokens > Spacing
**Auto-fix**: ⏸️ Richiede conferma

---

## Fix Applicati

✅ X fix applicati automaticamente
⏸️ X fix richiedono conferma manuale
📝 X suggerimenti per miglioramenti

## Coerenza UI/UX Score: XX%

## Next Steps

1. Rivedi i fix applicati con `git diff`
2. Conferma o modifica i fix suggeriti
3. Considera i suggerimenti di miglioramento
4. Esegui i test per verificare che tutto funzioni
5. Committa le modifiche
```

---

### Step 5: Applicazione Fix

**🔴 Critical (Auto-fix automatico):**
- Design tokens violations
- Terminologia inglese nell'UI
- Accessibilità mancante (aria-label)

**Azione**: Usa `Edit` tool per applicare fix automaticamente

**🟡 Warning (Auto-fix con conferma):**
- Spacing non ottimale (ma funzionale)
- Naming conventions non perfette
- Pattern di interazione migliorabili

**Azione**: Presenta il fix proposto, chiedi conferma, applica se confermato

**🔵 Info (Suggerimenti):**
- Best practices generali
- Ottimizzazioni performance
- Refactoring suggeriti

**Azione**: Elenca nel report senza modificare codice

---

### Step 6: Output Finale

```
✨ UI/UX Check Completato!

📊 Statistiche:
- File analizzati: 25
- Problemi trovati: 18 (8 critical, 7 warning, 3 info)
- Fix applicati: 15
- Coerenza UI/UX: 85%

🎯 Prossimi passi:
1. Rivedi i fix con `git diff`
2. Testa l'applicazione
3. Committa le modifiche
```

---

## Quando Usare Questa Skill

Esegui `/ui-ux-check [percorso]` quando:
- Vuoi verificare codice UI esistente
- Hai dubbi sulla coerenza di un componente
- Dopo merge di codice da altri developer
- Prima di un code review
- Periodicamente per mantenere la qualità del codice

### Evoluzione del Design System
Man mano che il progetto cresce:
1. Aggiorna `ui-ux-guidelines.yaml` con nuovi pattern
2. Aggiungi nuovi componenti alla configurazione
3. Estendi la terminologia con nuovi termini
4. Documenta nuovi pattern in `UI_UX_GUIDE.md`

---

## Note Importanti

- **Backup**: Fai sempre `git commit` prima di eseguire fix automatici
- **Testing**: Dopo i fix, esegui test per verificare che tutto funzioni
- **Iterativo**: Usa la skill durante tutto lo sviluppo, non solo alla fine
- **Configurabile**: Personalizza `ui-ux-guidelines.yaml` secondo le esigenze del progetto
- **Non invasivo**: La skill non blocca lo sviluppo, suggerisce e applica fix quando appropriato

---

## Riferimenti

- Guidelines: `.claude/skills/ui-ux-check/ui-ux-guidelines.yaml`
- Documentazione: `.claude/skills/ui-ux-check/UI_UX_GUIDE.md`
- Esempi: `.claude/skills/ui-ux-check/examples/fixes-examples.md`
- Progetto: `specification.md`, `CLAUDE.md`
