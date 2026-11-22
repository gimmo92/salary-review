# Salary Review

App Vue 3 + Vite per il ciclo di salary review: budget a cascata, schede manager, approvazioni, lettere PDF ed export paghe. Estratta dal progetto `salary-transparency` (menu Compensation → Salary review) e resa autonoma.

## Comandi

- `npm run dev` — dev server su http://localhost:5174 (porta diversa da salary-transparency, che usa 5173, così il `localStorage` non si mescola)
- `npm run build` — build in `dist/`
- `npm run preview` — anteprima della build

Non ci sono test né linter configurati: dopo una modifica verifica almeno con `npm run build`.

## Struttura

- `src/App.vue` — shell minimale: intestazione con nome azienda (salvato in `localStorage`, chiave `salary-review:company-name`) e il componente `SalaryReview`.
- `src/components/SalaryReview.vue` — tutta la UI e lo stato. Tab: **Budget**, **Schede manager**, **Approvazioni**, **Lettere e paghe**. Gestisce import Excel (`xlsx`) dell'organico e persistenza in `localStorage`.
- `src/lib/salaryReview.js` — logica pura del ciclo: impostazioni (`defaultSettings`, `mergeSettings`), organico (`buildOrg`, `rowsToPeople`), pool e allocazione (`cyclePool`, `planAllocation`, `effectiveBudget`, `splitLevers`), matrice performance (`performanceBand`, `recommendedPct`), vincoli e valutazione schede (`evaluateCard`, `submitBlockers`), workflow di approvazione (`canEdit`/`canSubmit`/`canApprove`/`canReject`/`canReturn`), gap di genere, export CSV (`payrollCsv`, `criteriaCsv`, `hierarchyTemplateCsv`).
- `src/lib/salaryReviewLetters.js` — generazione lettere PDF con `jspdf`, caricata in lazy import dal componente.
- `src/lib/salaryMetrics.js` — metriche retributive e gap di genere condivise (copia da salary-transparency).

## Concetti chiave

- **Cascata piatta vs gerarchica** (`settings.cascadeMode`): `'flat'` è il default, ogni scheda riceve la sua quota dal pool. `'hierarchy'` dà il pool ai responsabili di primo livello, che lo dividono verso il basso. Le nuove opzioni devono restare disattivabili e i default devono riprodurre il comportamento della cascata piatta.
- **Dati**: senza import l'app usa l'organico demo (`demoSeed`, signature `'demo'`). L'import Excel salva le persone in `localStorage` sotto `IMPORT_STORAGE_KEY` (`salary-review-hierarchy`). Lo stato del ciclo è salvato per signature con `storageKey(signature)`.
- **Attori**: la vista cambia in base all'attore selezionato (HR, HRBP, manager). HR e HRBP vedono tutta l'azienda.
- La prop `sourcePeople` del componente esiste ancora (nell'app originale arrivava dal job grading tramite `peopleFromJobResults`), ma qui non viene passata.

## Convenzioni

- UI, testi, commenti e messaggi di commit in italiano.
- Tieni la logica di calcolo in `src/lib/salaryReview.js` come funzioni pure. Il componente si occupa solo di stato e rendering.
- Stili scoped nel componente. Le variabili CSS globali (`--border`, `--text-primary`, ecc.) sono definite in `index.html`.
- I riferimenti normativi (Dir. UE 2023/970) vanno nei commenti, non nella UI.
- Deploy: `vercel.json` riscrive tutte le route su `index.html` (SPA, nessuna API).
