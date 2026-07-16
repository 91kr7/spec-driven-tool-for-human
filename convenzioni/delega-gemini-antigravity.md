# Convenzione — Delega a Gemini via Google Antigravity (`agy`)

Quando l'umano chiede esplicitamente di **delegare lo step a Gemini / Google Antigravity**, l'orchestratore non lancia il subagent nativo dello step, ma lo delega al subagent-ponte **`sdd-gemini-runner`**, che esegue lo stesso ruolo attraverso la CLI **`agy`** (Google Antigravity).

Si applica **solo su richiesta esplicita** dell'umano (es. «fallo con Gemini», «delega ad Antigravity»). In assenza di richiesta → subagent nativo via `Task`, come sempre.

## Principio: isolare il contesto

L'output grezzo di Gemini è pesante e non deve sporcare il contesto dell'orchestratore. Perciò la delega **gira dentro un subagent** (`sdd-gemini-runner`): l'output di `agy` resta nel contesto del ponte; all'orchestratore torna solo un riepilogo schematico (percorsi dei file + esito + eventuali domande).

**L'orchestratore non apre mai i file scritti dal ponte** (né con `Read` né rileggendoli in altro modo): il riepilogo del ponte è l'unica fonte che consulta. Rileggerli vanifica l'isolamento e raddoppia il consumo di token per lo stesso contenuto.

## Chi fa cosa

- **Orchestratore** → invece del subagent nativo, lancia `sdd-gemini-runner` via `Task` passandogli **il nome del ruolo da delegare** (`sdd-analyst`, `sdd-planner`, `sdd-developer`, …), l'input dello step, la data corrente e il modello Gemini scelto. Gestisce come sempre l'intermediazione delle domande e le verifiche/gate previsti dal comando.
- **`sdd-gemini-runner`** → legge il file di prompt del ruolo (`${CLAUDE_PLUGIN_ROOT}/agents/<ruolo>.md`), lo inoltra a `agy` in print mode, e **scrive lui** i file che lo step deve produrre. Non reinterpreta il ruolo: fa da tubo. Il dettaglio operativo è nel suo prompt.

Il ponte non ricopia la logica dei ruoli: inoltra **lo stesso prompt** dell'agente nativo, aggiungendo solo il contratto di output (Gemini in print mode non scrive file → emette il contenuto come testo delimitato, che il ponte scrive su disco).

## Scelta del modello

- Default per ragionamento/planning → `Gemini 3.1 Pro (High)`.
- Default per compiti più leggeri → `Gemini 3.5 Flash (High)`.
- Verifica sempre il nome esatto con `agy models` (va passato tra virgolette: contiene spazi e parentesi).
- Se l'umano indica un modello, usa quello.

## Passi per l'orchestratore

1. Ricava la data corrente (`date +%Y-%m-%d`).
2. Scegli il modello (vedi sopra) o usa quello indicato dall'umano.
3. Lancia `sdd-gemini-runner` via `Task`, passandogli: ruolo da delegare, input dello step, data, modello, eventuali risposte dell'umano a domande precedenti.
4. Al rientro, tratta il riepilogo del ponte **come tratteresti l'output del subagent nativo**: stesse verifiche meccaniche, stessi gate, stessa intermediazione delle domande (convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/intermediazione-domande.md`). Per un nuovo giro (correzioni o risposte), rilancia `sdd-gemini-runner`. **Non leggere tu i file** che il ponte ha scritto: fidati del riepilogo, i controlli restano meccanici (esistenza dei percorsi dichiarati), mai di merito sul contenuto.
5. Nel riepilogo all'utente, segnala che lo step è stato prodotto **via Antigravity/Gemini** e con quale modello.

## Sicurezza e limiti

- **Solo print mode** → `agy -p` produce testo, non tocca il filesystem. I file li scrive il ponte. Niente `--dangerously-skip-permissions`, niente `agy` in modalità agente.
- **Il prompt esce dalla macchina** → viene inviato ai server del provider del modello. **Mai** segreti, credenziali o dati sensibili nel prompt.
- **Print mode ≠ subagent completo** → un prompt one-shot non fa ricerca web né iterazione multi-step con tool. Per step che li richiedono davvero (es. tendenze di mercato in `sdd-analyst`), valuta se la delega è adeguata o mantieni il subagent nativo.
- **Non-determinismo** → l'output può variare tra run; il ponte valida sempre prima di scrivere.

## Troubleshooting

- `command not found: agy` → percorso assoluto `~/.local/bin/agy` o verifica il PATH.
- Timeout in print mode → alza `--print-timeout` (es. `10m`).
- Nome modello non valido → copia il nome **esatto** da `agy models`.
- Output con testo extra o file non conformi → il ponte rilancia rafforzando il contratto di output.
