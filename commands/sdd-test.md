---
description: Scrive ed esegue i test del lotto appena implementato, derivandoli dai requisiti e dalle spec dei componenti. Delega a un subagent Sonnet con ragionamento esteso.
argument-hint: "<percorso della cartella del piano, es. .sdd/plan-gestione_biblioteca>"
---

# /sdd-test — Test di un lotto

Scrive ed esegue i test del lotto in stato `implementato` del piano indicato in `$ARGUMENTS`, delegando al subagent `sdd-tester` (Sonnet, ragionamento esteso).

## Ruolo

- Tu (la sessione principale) sei l'**orchestratore**: non sei tu a scrivere i test.
- I test li scrive ed esegue il subagent `sdd-tester`.
- Tu gestisci: la scelta del lotto, lo stato su `lotti.md`, l'intermediazione delle domande e il referto finale.

## Passi

1. Leggi `lotti.md` nella cartella del piano e individua il lotto in stato `implementato`. Se non c'è, riporta all'utente lo stato della tabella e fermati.
2. Ricava la data corrente in formato ISO-8601 con `date +%Y-%m-%d`.
3. Lancia il subagent `sdd-tester` via Task, passandogli:
   - il percorso del file del lotto (`lotti/lotto-<slug>.md`)
   - il percorso di `requirements.md`
   - la data corrente
4. Se il subagent restituisce domande per l'umano → applica la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/intermediazione-domande.md` (ruolo orchestratore): poni le domande all'utente, riprendi lo stesso subagent con `SendMessage` e ripeti finché non restano domande.
5. Al rientro, valuta il referto:
   - **tutti i test verdi** → porta tu lo stato del lotto a `testato` in `lotti.md` (in dubbio, rilancia i comandi di test indicati in `.sdd/.archi` per conferma).
   - **test rossi per difetti del codice** → lo stato resta `implementato`; il referto va all'utente, che decide come correggere (nuovo giro di sviluppo o intervento manuale).
6. Riporta all'utente, in forma schematica:
   - i test scritti (quanti e dove) e l'esito dell'esecuzione
   - i difetti rilevati, ciascuno con il requisito o la spec violati
   - se tutto è verde → ricorda la **checklist di collaudo** (colonna «Collaudo umano» del lotto): il collaudo resta tuo
