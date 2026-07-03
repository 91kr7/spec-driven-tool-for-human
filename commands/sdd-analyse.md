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
3. Se il subagent restituisce **domande per l'umano**:
   - ponile all'utente (sei tu l'intermediario)
   - raccogli le risposte
   - **riprendi lo stesso subagent** (resume / `SendMessage`, NON un nuovo Task) passandogli le risposte → mantiene il contesto del primo giro e finalizza il file senza segnaposto
   - ripeti finché non restano domande
4. Riporta all'utente, in forma schematica:
   - percorso del file di analisi
   - tipo → nuova / correzione / evolutiva
