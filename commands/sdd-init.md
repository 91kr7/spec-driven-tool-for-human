---
description: Inizializza lo scheletro architetturale di un'app da una descrizione in linguaggio naturale dello stack. Crea l'architettura su disco e produce .sdd/.archi. Delega a un subagent Sonnet con ragionamento esteso.
argument-hint: "<descrizione dello stack: tecnologie, linguaggio, build tool, ...>"
---

# /sdd-init — Inizializzazione architettura

Delega la creazione dell'architettura descritta in `$ARGUMENTS` al subagent `sdd-architect` (Sonnet, ragionamento esteso).

## Ruolo

- Tu (la sessione principale) sei l'**orchestratore**: non sei tu a creare l'architettura.
- La crea il subagent `sdd-architect`.
- Tu fai da **intermediario** tra il subagent e l'umano per le domande.

## Passi

1. Ricava la data corrente in formato ISO-8601 con `date +%Y-%m-%d`.
2. Lancia il subagent `sdd-architect` via Task, passandogli la descrizione dello stack (`$ARGUMENTS`) e la data corrente.
3. Se il subagent restituisce domande per l'umano → applica la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/intermediazione-domande.md` (ruolo orchestratore): poni le domande all'utente, riprendi lo stesso subagent con `SendMessage` e ripeti finché non restano domande.
4. Riporta all'utente, in forma schematica:
   - il percorso del `.archi` prodotto
   - lo scheletro creato (cartelle e file principali)
