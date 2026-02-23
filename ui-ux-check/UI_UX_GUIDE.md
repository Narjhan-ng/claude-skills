# Guida UI/UX - Portale Wholesale

> Design System e linee guida per il Portale Wholesale Telecomunicazioni

**Versione:** 1.0.0
**Ultima modifica:** 2026-01-28

---

## Indice

1. [Introduzione](#introduzione)
2. [Design Tokens](#design-tokens)
3. [Componenti](#componenti)
4. [Pattern di Interazione](#pattern-di-interazione)
5. [Terminologia](#terminologia)
6. [Accessibilità](#accessibilità)
7. [Best Practices](#best-practices)
8. [FAQ](#faq)

---

## Introduzione

Questa guida definisce il design system del Portale Wholesale per mantenere **coerenza visiva, UX consistente e accessibilità** in tutto il progetto.

### Obiettivi del Design System

- **Coerenza**: Stessi pattern UI in tutto il portale
- **Efficienza**: Componenti riutilizzabili riducono il tempo di sviluppo
- **Accessibilità**: WCAG AA compliance per tutti gli utenti
- **Manutenibilità**: Modifiche centralizzate propagate a tutto il sistema
- **Qualità**: Standard elevati di codice e UX

### Come Usare Questa Guida

- **Sviluppatori**: Riferimento durante l'implementazione UI
- **Designer**: Linee guida per mockup e prototipi
- **QA**: Checklist per test UI/UX

---

## Design Tokens

I design tokens sono variabili che definiscono lo stile visivo del progetto. **Mai hardcodare valori** - usa sempre i token.

### Colori

#### Palette Primaria

| Token | Valore | Uso | Esempio |
|-------|--------|-----|---------|
| `--color-primary` | `#003366` | Brand, CTA principali | Bottoni primari, link |
| `--color-secondary` | `#0066CC` | Elementi secondari | Bottoni secondari, badges |

#### Stati

| Token | Valore | Uso |
|-------|--------|-----|
| `--color-success` | `#28A745` | Operazioni completate, validazione OK |
| `--color-warning` | `#FFC107` | Avvisi, attenzione richiesta |
| `--color-error` | `#DC3545` | Errori, validazione fallita |
| `--color-info` | `#17A2B8` | Informazioni neutre |

#### Testo

| Token | Valore | Uso |
|-------|--------|-----|
| `--color-text-primary` | `#212529` | Testo principale |
| `--color-text-secondary` | `#6C757D` | Testo secondario, descrizioni |
| `--color-text-muted` | `#ADB5BD` | Testo disabilitato, placeholder |

#### Esempi d'Uso

```css
/* ❌ NON fare così */
.button {
  background: #003366;
  color: #FFFFFF;
}

/* ✅ Fare così */
.button {
  background: var(--color-primary);
  color: var(--color-text-inverse);
}
```

---

### Spacing

Tutti gli spacing devono essere **multipli di 8px** per mantenere una griglia armoniosa.

#### Scale di Spacing

| Token | Valore | Uso |
|-------|--------|-----|
| `--spacing-xs` | `4px` | Spacing molto piccolo |
| `--spacing-sm` | `8px` | Spacing piccolo |
| `--spacing-md` | `16px` | Spacing standard componenti |
| `--spacing-lg` | `24px` | Spacing tra sezioni |
| `--spacing-xl` | `32px` | Spacing grande tra sezioni |
| `--spacing-2xl` | `48px` | Spacing molto grande |
| `--spacing-3xl` | `64px` | Spacing header/footer |

#### Regola Base

- ✅ Usa: `8px`, `16px`, `24px`, `32px`
- ❌ Evita: `15px`, `18px`, `25px`, `30px`

#### Esempi

```css
/* ❌ NON fare così */
.card {
  padding: 15px;
  margin: 25px 0;
}

/* ✅ Fare così */
.card {
  padding: var(--spacing-md);  /* 16px */
  margin: var(--spacing-lg) 0;  /* 24px */
}
```

---

### Tipografia

#### Font Family

```css
--font-primary: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
--font-monospace: 'Fira Code', 'Consolas', monospace;
```

#### Scale Tipografica

| Token | Dimensione | Uso |
|-------|------------|-----|
| `--font-xs` | `12px` | Label piccole, note |
| `--font-sm` | `14px` | Testo secondario |
| `--font-base` | `16px` | Testo principale |
| `--font-lg` | `18px` | Sottotitoli |
| `--font-xl` | `20px` | Titoli sezione |
| `--font-2xl` | `24px` | Titoli grandi |
| `--font-3xl` | `32px` | Heading principale |

#### Font Weight

| Token | Valore | Uso |
|-------|--------|-----|
| `--font-regular` | `400` | Testo normale |
| `--font-medium` | `500` | Enfasi leggera |
| `--font-semibold` | `600` | Bottoni, label |
| `--font-bold` | `700` | Titoli, enfasi forte |

#### Esempi

```css
h1 {
  font-size: var(--font-3xl);
  font-weight: var(--font-bold);
  line-height: 1.25;
}

p {
  font-size: var(--font-base);
  font-weight: var(--font-regular);
  line-height: 1.5;
}
```

---

## Componenti

### Button

I bottoni sono elementi interattivi per azioni primarie e secondarie.

#### Varianti

##### Primary Button
Usa per l'azione principale della pagina.

```jsx
<button className="btn btn-primary">
  Salva
</button>
```

##### Secondary Button
Usa per azioni secondarie.

```jsx
<button className="btn btn-secondary">
  Annulla
</button>
```

##### Outline Button
Usa per azioni terziarie o in contesti con sfondo colorato.

```jsx
<button className="btn btn-outline">
  Visualizza Dettagli
</button>
```

##### Text Button
Usa per azioni meno importanti, senza sfondo.

```jsx
<button className="btn btn-text">
  Modifica
</button>
```

#### Stati

Tutti i bottoni devono implementare questi stati:

```jsx
// Stato normale
<button className="btn btn-primary">Salva</button>

// Stato loading
<button className="btn btn-primary" disabled>
  <LoadingSpinner size="sm" /> Salvataggio...
</button>

// Stato disabled
<button className="btn btn-primary" disabled>
  Salva
</button>
```

#### Accessibilità

**Obbligatorio:** Ogni bottone deve avere `aria-label` descrittivo.

```jsx
// ❌ NON fare così
<button onClick={handleDelete}>
  <TrashIcon />
</button>

// ✅ Fare così
<button onClick={handleDelete} aria-label="Elimina cliente">
  <TrashIcon />
</button>
```

---

### Input

Gli input sono campi per inserimento dati utente.

#### Struttura Base

```jsx
<div className="form-field">
  <label htmlFor="email" className="form-label">
    Email *
  </label>
  <input
    id="email"
    name="email"
    type="email"
    className="input"
    aria-describedby="email-error"
    aria-invalid={hasError}
    required
  />
  {hasError && (
    <ErrorMessage id="email-error">
      Email non valida
    </ErrorMessage>
  )}
</div>
```

#### Stati

```css
/* Focus */
.input:focus {
  border-color: var(--color-primary);
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

/* Error */
.input[aria-invalid="true"] {
  border-color: var(--color-error);
}

/* Disabled */
.input:disabled {
  background: var(--color-bg-alt);
  cursor: not-allowed;
  opacity: 0.6;
}
```

#### Validazione

La validazione deve avvenire **onBlur** e mostrare errori **inline sotto il campo**.

```jsx
const [errors, setErrors] = useState({});

const handleBlur = (field) => {
  const error = validateField(field, formData[field]);
  setErrors({ ...errors, [field]: error });
};

return (
  <input
    name="email"
    onBlur={() => handleBlur('email')}
    aria-invalid={!!errors.email}
    aria-describedby={errors.email ? 'email-error' : undefined}
  />
);
```

---

### Card

Le card raggruppano contenuti correlati.

#### Struttura

```jsx
<div className="card">
  <div className="card-header">
    <h3>Informazioni Cliente</h3>
  </div>
  <div className="card-body">
    <p>Nome: Mario Rossi</p>
    <p>Email: mario@example.com</p>
  </div>
  <div className="card-footer">
    <button className="btn btn-text">Modifica</button>
    <button className="btn btn-primary">Visualizza</button>
  </div>
</div>
```

#### Style Guidelines

- Padding interno: `16px` (card-body)
- Border radius: `8px`
- Shadow: `md` (hover: `lg`)
- Background: `var(--color-bg-main)`

---

### Modal

Le modal sono dialog per azioni che richiedono focus completo.

#### Quando Usare

- ✅ Azioni distruttive (conferma eliminazione)
- ✅ Form complessi che richiedono focus
- ✅ Visualizzazione dettagli che non giustificano nuova pagina

- ❌ Notifiche semplici (usa Toast)
- ❌ Navigation principale (usa routing)

#### Struttura

```jsx
<div className="modal-overlay" onClick={handleClose}>
  <div
    className="modal"
    role="dialog"
    aria-modal="true"
    aria-labelledby="modal-title"
    onClick={(e) => e.stopPropagation()}
  >
    <div className="modal-header">
      <h2 id="modal-title">Conferma Eliminazione</h2>
      <button
        className="modal-close"
        onClick={handleClose}
        aria-label="Chiudi modal"
      >
        ×
      </button>
    </div>
    <div className="modal-body">
      Sei sicuro di voler eliminare questo cliente?
    </div>
    <div className="modal-footer">
      <button className="btn btn-secondary" onClick={handleClose}>
        Annulla
      </button>
      <button className="btn btn-danger" onClick={handleConfirm}>
        Elimina
      </button>
    </div>
  </div>
</div>
```

#### Features Obbligatorie

- [ ] Close button con `aria-label`
- [ ] Overlay cliccabile per chiudere
- [ ] ESC key handler
- [ ] Focus trap (focus rimane dentro la modal)
- [ ] Scroll lock sul body quando aperta

---

### Table

Le tabelle mostrano dati strutturati.

#### Struttura

```jsx
<div className="table-responsive">
  <table className="table table-striped table-hover">
    <thead>
      <tr>
        <th scope="col">Nome</th>
        <th scope="col">Email</th>
        <th scope="col">Stato</th>
        <th scope="col">Azioni</th>
      </tr>
    </thead>
    <tbody>
      {customers.map(customer => (
        <tr key={customer.id}>
          <td>{customer.name}</td>
          <td>{customer.email}</td>
          <td>
            <StatusBadge status={customer.status} />
          </td>
          <td>
            <button className="btn btn-sm btn-text">Modifica</button>
          </td>
        </tr>
      ))}
    </tbody>
  </table>
</div>
```

#### Responsive

Tabelle devono essere **responsive** su mobile:

```css
.table-responsive {
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}

@media (max-width: 768px) {
  .table {
    font-size: var(--font-sm);
  }
}
```

---

## Pattern di Interazione

### Loading States

#### Spinner

Usa per operazioni async brevi (< 5 secondi).

```jsx
{isLoading && <LoadingSpinner size="md" />}
```

#### Skeleton Loader

Usa per initial data load, preserva il layout.

```jsx
{isLoading ? (
  <SkeletonLoader lines={3} />
) : (
  <UserProfile data={user} />
)}
```

#### Button Loading

Mostra feedback diretto sull'azione.

```jsx
<button className="btn btn-primary" disabled={isSubmitting}>
  {isSubmitting ? (
    <>
      <LoadingSpinner size="sm" /> Salvataggio...
    </>
  ) : (
    'Salva'
  )}
</button>
```

---

### Error Handling

#### Toast Notification

Usa per errori/successi generali.

```jsx
toast.error("Si è verificato un errore durante il salvataggio", {
  duration: 4000,
  position: "top-right"
});

toast.success("Cliente salvato con successo", {
  duration: 3000,
  position: "top-right"
});
```

**Configurazione:**
- Position: `top-right`
- Duration: `4000ms` (error/warning), `3000ms` (success/info)
- Dismissible: `true`

#### Inline Error

Usa per validazione form.

```jsx
<ErrorMessage>
  Il campo email è obbligatorio
</ErrorMessage>
```

#### Error Boundary

Usa per errori React component.

```jsx
<ErrorBoundary fallback={<ErrorFallback />}>
  <UserDashboard />
</ErrorBoundary>
```

---

### Form Validation

#### Principi

1. **Validate onBlur**: Prima validazione quando l'utente esce dal campo
2. **Validate onChange dopo primo blur**: Feedback immediato dopo primo errore
3. **Inline errors**: Mostra errori sotto il campo
4. **Disable submit quando errori**: Previeni submit con dati invalidi
5. **Success feedback**: Mostra checkmark per campi validi

#### Esempio Completo

```jsx
const [formData, setFormData] = useState({ email: '' });
const [errors, setErrors] = useState({});
const [touched, setTouched] = useState({});

const validateEmail = (email) => {
  if (!email) return "Email è obbligatoria";
  if (!/\S+@\S+\.\S+/.test(email)) return "Email non valida";
  return null;
};

const handleBlur = (field) => {
  setTouched({ ...touched, [field]: true });
  const error = validateEmail(formData[field]);
  setErrors({ ...errors, [field]: error });
};

const handleChange = (field, value) => {
  setFormData({ ...formData, [field]: value });
  // Valida onChange solo se già toccato
  if (touched[field]) {
    const error = validateEmail(value);
    setErrors({ ...errors, [field]: error });
  }
};

return (
  <div className="form-field">
    <label htmlFor="email">Email *</label>
    <input
      id="email"
      type="email"
      value={formData.email}
      onChange={(e) => handleChange('email', e.target.value)}
      onBlur={() => handleBlur('email')}
      aria-invalid={!!errors.email}
      aria-describedby={errors.email ? 'email-error' : undefined}
    />
    {errors.email && (
      <ErrorMessage id="email-error">{errors.email}</ErrorMessage>
    )}
  </div>
);
```

---

## Terminologia

**Regola fondamentale:** L'interfaccia utente deve essere **completamente in italiano**.

### Entità Comuni

| ❌ Inglese | ✅ Italiano |
|-----------|-------------|
| Customer | Cliente |
| Wholesaler | Wholesaler (o WS) |
| Provider | Fornitore |
| Admin | Admin (o A) |
| Invoice | Fattura |
| Ticket | Ticket |
| Request | Richiesta |
| Status | Stato |

### Azioni

| ❌ Inglese | ✅ Italiano |
|-----------|-------------|
| Create | Crea |
| Edit | Modifica |
| Delete | Elimina |
| Save | Salva |
| Cancel | Annulla |
| Confirm | Conferma |
| Send | Invia |
| Download | Scarica |

### Stati

| ❌ Inglese | ✅ Italiano |
|-----------|-------------|
| Pending | In attesa |
| In Progress | In elaborazione |
| Completed | Completato |
| Failed | Fallito |
| Cancelled | Annullato |

### Consistenza

- ✅ Usa sempre **"Cliente"**, mai "Utente" o "Customer"
- ✅ Usa sempre **"Fattura"**, mai "Invoice"
- ✅ Usa sempre **"Salva"**, mai "Save" o "Conferma"

---

## Accessibilità

### Principi WCAG AA

Il portale deve essere conforme a **WCAG 2.1 Level AA**.

#### Semantic HTML

```jsx
// ❌ NON fare così
<div onClick={handleClick}>Click me</div>

// ✅ Fare così
<button onClick={handleClick}>Click me</button>
```

#### ARIA Labels

```jsx
// Bottone con solo icona
<button aria-label="Chiudi modal">
  <CloseIcon />
</button>

// Link con descrizione
<a href="/clienti" aria-label="Vai alla lista clienti">
  Clienti
</a>

// Form con errori
<input
  aria-invalid={hasError}
  aria-describedby="field-error"
/>
<span id="field-error" role="alert">
  Campo obbligatorio
</span>
```

#### Keyboard Navigation

- **Tab**: Naviga tra elementi interattivi
- **Enter/Space**: Attiva bottoni/link
- **Esc**: Chiudi modal/dropdown
- **Arrow keys**: Naviga in dropdown/combobox

```jsx
// Gestione Esc per chiudere modal
useEffect(() => {
  const handleEsc = (e) => {
    if (e.key === 'Escape') handleClose();
  };
  window.addEventListener('keydown', handleEsc);
  return () => window.removeEventListener('keydown', handleEsc);
}, []);
```

#### Focus Visible

```css
/* Focus visibile per keyboard navigation */
*:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}
```

#### Color Contrast

- **Testo normale**: Contrasto minimo 4.5:1
- **Testo grande** (>=18px o bold >=14px): Contrasto minimo 3:1

Usa tool come [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) per verificare.

---

## Best Practices

### Do's ✅

- Usa design tokens per tutti i valori CSS
- Implementa loading states per tutte le operazioni async
- Mostra errori inline con messaggi chiari
- Usa terminologia italiana consistente
- Implementa accessibilità (ARIA, keyboard navigation)
- Testa su mobile e desktop
- Valida form onBlur con feedback immediato
- Usa semantic HTML

### Don'ts ❌

- Non hardcodare colori, spacing, font
- Non usare inline styles
- Non ignorare loading/error states
- Non mescolare inglese e italiano nell'UI
- Non dimenticare alt text per immagini
- Non usare `<div>` per elementi interattivi
- Non saltare focus management nelle modal
- Non usare spacing non multipli di 8px

---

## FAQ

### Come scelgo tra Button primary e secondary?

- **Primary**: Azione principale della pagina/sezione (es: "Salva", "Crea Cliente")
- **Secondary**: Azioni alternative (es: "Annulla", "Indietro")

**Regola**: Max 1 primary button per sezione.

### Quando usare Toast vs Inline Error?

- **Toast**: Errori/successi generali, operazioni completate (es: "Cliente salvato")
- **Inline Error**: Validazione form specifica per campo (es: "Email non valida")

### Come gestisco tabelle molto grandi?

- Implementa **paginazione** (max 20-50 righe per pagina)
- Aggiungi **filtri** e **ricerca**
- Usa **sticky header** per tabelle lunghe
- Considera **virtualizzazione** per tabelle con 1000+ righe

### Posso usare colori custom per casi speciali?

No. **Tutti i colori devono essere nei design tokens**. Se serve un nuovo colore:
1. Aggiungi il token in `ui-ux-guidelines.yaml`
2. Documenta l'uso in questa guida
3. Usa il nuovo token nel codice

### Come testo l'accessibilità?

1. **Keyboard only**: Naviga il sito senza mouse
2. **Screen reader**: Usa NVDA (Windows) o VoiceOver (Mac)
3. **Zoom**: Testa a 200% zoom
4. **Contrast checker**: Verifica contrasto colori
5. **axe DevTools**: Esegui audit automatici

---

## Risorse

- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [MDN Accessibility](https://developer.mozilla.org/en-US/docs/Web/Accessibility)
- [A11y Project Checklist](https://www.a11yproject.com/checklist/)

---

**Nota**: Questa guida è un documento vivo. Contribuisci con miglioramenti e nuovi pattern man mano che il progetto evolve.
