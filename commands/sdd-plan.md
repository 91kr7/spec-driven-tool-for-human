---
description: Trasforma una spec di business in un piano tecnico a lotti (feature, requisiti, interventi) in .sdd/plan-<slug>/, con validazione umana dei requisiti. Delega a un subagent Opus con ragionamento esteso.
argument-hint: "<percorso del file di spec/analisi di business>"
---

# /sdd-plan — Piano tecnico

Delega la pianificazione tecnica della spec `$ARGUMENTS` al subagent `sdd-planner` (Opus, ragionamento esteso).

## Ruolo

- Tu (sessione principale) sei l'**orchestratore** → non scrivi tu il piano.
- Il piano lo produce il subagent `sdd-planner`.
- Fai da **intermediario** tra il subagent e l'umano per domande e validazione dei requisiti.

## Passi

1. Ricava la data corrente (ISO-8601) → `date +%Y-%m-%d`.
2. Lancia il subagent `sdd-planner` via Task, passandogli:
   - il percorso della spec → `$ARGUMENTS`
   - la data corrente
3. Il subagent si ferma per la **validazione dei requisiti** (ed eventuali domande):
   - presenta all'utente `requirements.md` (percorso + sintesi schematica delle feature/REQ)
   - raccogli conferma o correzioni
   - applica la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/intermediazione-domande.md` (ruolo orchestratore): riprendi lo stesso subagent con `SendMessage`, ripeti finché l'umano non valida.
   > `${CLAUDE_PLUGIN_ROOT}` = radice del plugin; se non impostata, cerca sotto `~/.claude`.
4. Prodotti i lotti, il subagent si ferma per la **validazione della copertura REQ ↔ INT**:
   - presenta all'utente `lotti.md` (controllo di copertura incluso) e i file dei lotti
   - l'umano valida a mano; raccogli conferma o correzioni
   - stessa convenzione del passo 3 → riprendi lo stesso subagent finché l'umano non valida.
5. Riporta all'utente, in forma schematica:
   - percorso della cartella del piano
   - lotti prodotti, con ordine di esecuzione (dipendenze)
