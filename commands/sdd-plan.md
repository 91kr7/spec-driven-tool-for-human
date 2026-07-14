---
description: Trasforma una spec di business in un piano tecnico a lotti (feature, requisiti, interventi) in .sdd/plans/plan-<slug>/, con validazione umana dei requisiti. Delega a un subagent Opus con ragionamento esteso.
argument-hint: "<percorso del file di spec/analisi di business>"
---

# /sdd-plan — Piano tecnico

Delega la pianificazione tecnica della spec `$ARGUMENTS` al subagent `sdd-planner` (Opus, ragionamento esteso).

## Ruolo

- Tu (la sessione principale) sei l'**orchestratore**: non sei tu a scrivere il piano.
- Il piano lo produce il subagent `sdd-planner`.
- Tu fai da **intermediario** tra il subagent e l'umano, per le domande e per le due validazioni.

## Passi

1. Ricava la data corrente in formato ISO-8601 con `date +%Y-%m-%d`.
2. Lancia il subagent `sdd-planner` via Task, passandogli il percorso della spec (`$ARGUMENTS`) e la data corrente.
3. Il subagent si ferma per la **validazione dei requisiti** (ed eventuali domande):
   - presenta all'utente il percorso di `requirements.md` e una sintesi schematica delle feature e dei requisiti
   - raccogli la conferma o le correzioni
   - applica la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/intermediazione-domande.md` (ruolo orchestratore): riprendi lo stesso subagent con `SendMessage` e ripeti finché l'umano non valida.
4. Quando i lotti sono pronti, il subagent si ferma per la **validazione della copertura REQ ↔ INT**:
   - presenta all'utente i **percorsi** dei file prodotti e una sintesi schematica della copertura (non incollare i file interi in chat)
   - l'umano valida a mano; raccogli la conferma o le correzioni
   - stessa convenzione del passo 3: riprendi lo stesso subagent finché l'umano non valida.
5. Riporta all'utente, in forma schematica:
   - il percorso della cartella del piano
   - l'elenco dei lotti prodotti, con l'ordine di esecuzione (dipendenze)

## Delega a Gemini (su richiesta)

Se l'utente chiede di **delegare la pianificazione a Gemini / Google Antigravity** → non lanciare `sdd-planner`: delega al subagent-ponte `sdd-gemini-runner` (ruolo `sdd-planner`) secondo la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/delega-gemini-antigravity.md`.
