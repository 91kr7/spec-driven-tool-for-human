---
description: Analizza in ottica di business una richiesta (anche banale) e produce un'analisi su file, primo passo del workflow spec-driven. Delega a un subagent Opus con ragionamento esteso.
argument-hint: "<richiesta da analizzare, testo libero>"
---

# /sdd-analyse — Analisi di business

Primo comando del workflow spec-driven.
Delega l'analisi della richiesta `$ARGUMENTS` al subagent `sdd-analyst` (Opus, ragionamento esteso).

## Ruolo

- Tu (la sessione principale) sei l'**orchestratore**: non sei tu a scrivere l'analisi.
- L'analisi la produce il subagent `sdd-analyst`.
- Tu fai da **intermediario** tra il subagent e l'umano per le domande.

## Passi

1. Ricava la data corrente in formato ISO-8601 con `date +%Y-%m-%d`.
2. Lancia il subagent `sdd-analyst` via Task, passandogli la richiesta (`$ARGUMENTS`) e la data corrente.
3. Se il subagent restituisce domande per l'umano → applica la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/intermediazione-domande.md` (ruolo orchestratore): poni le domande all'utente, riprendi lo stesso subagent con `SendMessage` e ripeti finché non restano domande.
4. Riporta all'utente, in forma schematica:
   - il percorso del file di analisi prodotto
   - il tipo di analisi: nuova, correzione o evolutiva

## Delega a Gemini (su richiesta)

Se l'utente chiede di **delegare l'analisi a Gemini / Google Antigravity** → non lanciare `sdd-analyst`: delega al subagent-ponte `sdd-gemini-runner` (ruolo `sdd-analyst`) secondo la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/delega-gemini-antigravity.md`.
