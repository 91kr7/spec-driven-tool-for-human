---
description: Analizza in ottica di business una richiesta (anche banale) e produce un'analisi su file, primo passo del workflow spec-driven. Delega a un subagent Opus con ragionamento esteso.
argument-hint: "<richiesta da analizzare, testo libero>"
---

# /sdd-analyse — Analisi di business

Primo comando del workflow spec-driven.
Delega l'analisi di `$ARGUMENTS` al subagent `sdd-analyst` (Opus, ragionamento esteso).

## Ruolo

- Tu (sessione principale) sei l'**orchestratore** → non scrivi tu l'analisi.
- L'analisi la produce il subagent `sdd-analyst`.
- Fai da **intermediario** tra il subagent e l'umano per le domande.

## Passi

1. Ricava la data corrente (ISO-8601) → `date +%Y-%m-%d`.
2. Lancia il subagent `sdd-analyst` via Task, passandogli:
   - la richiesta → `$ARGUMENTS`
   - la data corrente
3. Domande per l'umano → applica la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/intermediazione-domande.md` (ruolo orchestratore): poni le domande, riprendi lo stesso subagent con `SendMessage`, ripeti finché non restano domande.
   > `${CLAUDE_PLUGIN_ROOT}` = radice del plugin; se non impostata, cerca sotto `~/.claude`.
4. Riporta all'utente, in forma schematica:
   - percorso del file di analisi
   - tipo → nuova / correzione / evolutiva
