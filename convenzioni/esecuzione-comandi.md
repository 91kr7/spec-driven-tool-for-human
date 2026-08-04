# Convenzione — Esecuzione di comandi (build e test)

L'output dei comandi entra nel contesto dell'agent e costa token: la verbosità va ridotta al minimo.

## Regole

- Esegui build e test nella **variante meno verbosa** disponibile; se `.archi` registra i comandi silenziosi, usa quelli.
- Esempi di varianti silenziose:
  - Maven → `mvn -q`
  - Gradle → `gradle -q`
  - Vitest → `vitest run --reporter=dot`
  - Jest → `jest --silent`
  - Karma/Angular → `ng test --watch=false` con reporter minimale
  - Playwright → `--reporter=dot` (o `line`)
  - npm → `npm --silent run <script>`
- In caso di fallimento → NON rilanciare tutto in modalità verbosa: rilancia **solo la parte fallita** (il singolo test o modulo) con il dettaglio necessario.
- Se l'output resta comunque lungo → filtralo (es. `| tail`, `grep` sugli errori) invece di leggerlo intero.

## Ambito di esecuzione: prima il modulo, la run globale solo sull'ultimo lotto

- Mentre lavori su un modulo → esegui **solo i test di quel modulo** (filtri idiomatici: `mvn -q -Dtest=...`, percorso o pattern per Vitest/Jest, `--grep` per Playwright).
- La **run globale** dell'intera suite (tutti i moduli) si fa **una volta sola, alla fine del piano** → **solo sull'ultimo lotto**, come verdetto di non-regressione sull'intero piano. Mai come ciclo di iterazione.
- Sui **lotti intermedi** → nessuna run globale: si chiude con i test del lotto (o dei moduli toccati) verdi. La non-regressione fra lotti si verifica tutta insieme alla fine.
- Chi è l'ultimo lotto lo stabilisce e lo comunica l'**orchestratore** (`/sdd-dev`): il subagent non lo deduce da sé.
- Se la run globale (ultimo lotto) trova un fallimento fuori dal modulo corrente → è una regressione: si corregge in ambito ristretto e si chiude con una nuova run globale verde.
