---
description: Implementa il prossimo lotto eleggibile di un piano tecnico: spec dei componenti, codice, indici e test, con gate umano sul collaudo. Delega a un subagent Sonnet con ragionamento esteso.
argument-hint: "<percorso della cartella del piano, es. .sdd/plan-gestione_biblioteca>"
---

# /sdd-dev — Implementazione di un lotto

Esegue **un lotto** del piano indicato in `$ARGUMENTS`, delegando l'implementazione al subagent `sdd-developer` (Sonnet, ragionamento esteso).

## Ruolo

- Tu (la sessione principale) sei l'**orchestratore**: non sei tu a implementare.
- L'implementazione la fa il subagent `sdd-developer`.
- Tu gestisci: la scelta del lotto, gli stati su `lotti.md`, il gate del collaudo, la verifica meccanica finale e l'intermediazione delle domande.

## Passi

1. Leggi `lotti.md` nella cartella del piano. Se il frontmatter riporta `stato: bozza`, fermati: il piano non è ancora validato dall'umano.
2. **Gate del collaudo**:
   - Se un lotto è in stato `implementato`, chiedi all'utente se lo ha collaudato (la colonna «Collaudo umano» dice cosa provare). Se sì, portalo tu a `collaudato`; se no, fermati: si collauda prima di andare avanti.
   - Se un lotto è in stato `in corso`, una run precedente si è interrotta: segnalalo all'utente e fermati, decide lui come procedere.
3. **Scegli il lotto** → il primo in stato `da fare` con tutte le dipendenze in stato `collaudato`. Se non ce n'è nessuno: se tutti i lotti sono `collaudato` il piano è completo, altrimenti riporta all'utente lo stato della tabella. In entrambi i casi fermati.
4. Ricava la data corrente in formato ISO-8601 con `date +%Y-%m-%d`.
5. Porta lo stato del lotto scelto a `in corso` in `lotti.md` (lo stato lo scrivi tu, mai il subagent).
6. Lancia il subagent `sdd-developer` via Task, passandogli:
   - il percorso del file del lotto (`lotti/lotto-<slug>.md`)
   - il percorso di `requirements.md`
   - la data corrente
7. Se il subagent restituisce domande per l'umano → applica la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/intermediazione-domande.md` (ruolo orchestratore): poni le domande all'utente, riprendi lo stesso subagent con `SendMessage` e ripeti finché non restano domande.
8. Al rientro, esegui la **verifica meccanica** (controlli di esistenza, non di merito):
   - ogni componente creato o modificato ha la sua riga nell'indice del modulo e la sua spec (convenzioni `indici.md` e `spec-componenti.md`);
   - un modulo nuovo ha la sua riga in `moduli.md`;
   - il subagent riporta build e test **verdi** (in dubbio, rilancia tu i comandi canonici indicati in `.sdd/.archi`).
   - Se manca qualcosa, riprendi lo stesso subagent con l'elenco preciso delle mancanze, finché la verifica non passa.
9. Porta lo stato del lotto a `implementato` in `lotti.md`.
10. Riporta all'utente, in forma schematica:
    - il lotto implementato e i componenti creati/modificati
    - l'esito di build e test
    - le divergenze tra piano e realtà segnalate dal subagent, se presenti
    - la **checklist di collaudo** (colonna «Collaudo umano» del lotto) → invitalo a collaudare prima del prossimo lotto
