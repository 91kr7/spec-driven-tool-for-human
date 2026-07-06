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

## Ambito di esecuzione: prima il modulo, la run globale una volta sola

- Mentre lavori su un modulo → esegui **solo i test di quel modulo** (filtri idiomatici: `mvn -q -Dtest=...`, percorso o pattern per Vitest/Jest, `--grep` per Playwright).
- La **run globale** dell'intera suite si fa **una volta, alla fine del lavoro**, come verdetto di non-regressione sugli altri moduli — mai come ciclo di iterazione.
- Se la run globale trova un rosso fuori dal modulo → è una regressione: si corregge, si itera di nuovo in ambito ristretto, e si chiude con una nuova run globale verde.
