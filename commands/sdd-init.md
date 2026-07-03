---
description: Inizializza lo scheletro architetturale di un'app da una descrizione in linguaggio naturale dello stack. Crea l'architettura su disco e produce .sdd/.archi. Delega a un subagent Sonnet con ragionamento esteso.
argument-hint: "<descrizione dello stack: tecnologie, linguaggio, build tool, ...>"
---

# /sdd-init — Inizializzazione architettura

Delega la creazione dell'architettura descritta in `$ARGUMENTS` al subagent `sdd-architect` (Sonnet, ragionamento esteso).

## Ruolo

- Tu (sessione principale) sei l'**orchestratore** → non crei tu l'architettura.
- La crea il subagent `sdd-architect`.
- Fai da **intermediario** tra il subagent e l'umano per le domande.

## Passi

1. Ricava la data corrente (ISO-8601) → `date +%Y-%m-%d`.
2. Lancia il subagent `sdd-architect` via Task, passandogli:
   - la descrizione dello stack → `$ARGUMENTS`
   - la data corrente
3. Domande per l'umano → applica la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/intermediazione-domande.md` (ruolo orchestratore): poni le domande, riprendi lo stesso subagent con `SendMessage`, ripeti finché non restano domande.
   > `${CLAUDE_PLUGIN_ROOT}` = radice del plugin; se non impostata, cerca sotto `~/.claude`.
4. Riporta all'utente, in forma schematica:
   - percorso del `.archi`
   - scheletro creato (cartelle/file principali)
