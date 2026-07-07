---
description: Implementa e testa il prossimo lotto eleggibile di un piano tecnico: sviluppo (spec, codice, indici) e subito dopo i test derivati dai contratti; a test verdi il lotto è certificato in automatico (collaudato), senza gate umano. Delega ai subagent sdd-developer e sdd-tester.
argument-hint: "<percorso della cartella del piano, es. .sdd/plan-gestione_biblioteca>"
---

# /sdd-dev — Sviluppo e test di un lotto

Esegue **un lotto** del piano indicato in `$ARGUMENTS`: prima lo sviluppo (subagent `sdd-developer`), poi **subito** i test (subagent `sdd-tester`).

## Ruolo

- Tu (la sessione principale) sei l'**orchestratore**: non implementi e non scrivi test.
- Lo sviluppo lo fa `sdd-developer`; i test li scrive ed esegue `sdd-tester`. Sono due subagent distinti di proposito: chi certifica non è chi ha scritto il codice.
- Tu gestisci: la scelta del lotto, gli stati su `lotti.md`, i gate d'ingresso e sui test rossi, le verifiche meccaniche e l'intermediazione delle domande.

## Passi

### Avvio

1. Leggi `lotti.md` nella cartella del piano. Se il frontmatter riporta `stato: bozza`, fermati: il piano non è ancora validato dall'umano.
2. Gate d'ingresso:
   - un lotto è in stato `testato` (residuo di una run precedente alla certificazione automatica) → i suoi test erano verdi: portalo a `collaudato` e prosegui.
   - un lotto è in stato `implementato` → sviluppo concluso ma test mancanti: salta direttamente alla fase Test per quel lotto.
   - un lotto è in stato `in corso` → una run precedente si è interrotta: segnalalo all'utente e fermati, decide lui come procedere.
3. Scegli il lotto → il primo in stato `da fare` con tutte le dipendenze in stato `collaudato`. Se non ce n'è nessuno: se tutti i lotti sono `collaudato` il piano è completo, altrimenti riporta all'utente lo stato della tabella. In entrambi i casi fermati.
4. Ricava la data corrente in formato ISO-8601 con `date +%Y-%m-%d`.

### Fase sviluppo

5. Porta lo stato del lotto a `in corso` in `lotti.md` (gli stati li scrivi tu, mai i subagent).
6. Lancia il subagent `sdd-developer` via Task, passandogli:
   - il percorso del file del lotto (`lotti/lotto-<slug>.md`)
   - il percorso di `requirements.md`
   - la data corrente
7. Se il subagent restituisce domande per l'umano → applica la convenzione `${CLAUDE_PLUGIN_ROOT}/convenzioni/intermediazione-domande.md` (ruolo orchestratore): poni le domande all'utente, riprendi lo stesso subagent con `SendMessage` e ripeti finché non restano domande.
8. Al rientro, esegui la **verifica meccanica** (controlli di esistenza, non di merito):
   - ogni componente creato o modificato ha la sua riga nell'indice del modulo e la sua spec in `specs/` (convenzione `indici.md`);
   - un modulo nuovo ha la sua riga in `moduli.md`;
   - il subagent riporta build **verde** e test preesistenti **verdi** — la non-regressione sui lotti già certificati (in dubbio, rilancia tu i comandi canonici indicati in `.sdd/.archi`).
   - Se manca qualcosa, riprendi lo stesso subagent con l'elenco preciso delle mancanze, finché la verifica non passa.
9. Porta lo stato del lotto a `implementato`.

### Fase test (subito dopo lo sviluppo)

10. Lancia il subagent `sdd-tester` via Task, passandogli:
    - il percorso del file del lotto (`lotti/lotto-<slug>.md`)
    - il percorso di `requirements.md`
    - la data corrente
11. Domande del tester → stessa convenzione di intermediazione del passo 7.
12. Al rientro, valuta il referto:
    - **tutti i test verdi** → porta lo stato del lotto direttamente a `collaudato`: i test verdi certificano il lotto, senza gate di collaudo umano (in dubbio, rilancia i comandi di test indicati in `.sdd/.archi` per conferma).
    - **test rossi per difetti del codice** → presenta il referto all'utente e chiedigli come procedere: mandare le correzioni al developer (riprendi `sdd-developer` con l'elenco dei difetti, poi ripeti la fase Test) oppure fermarsi qui (lo stato resta `implementato`). Nessun giro di correzione parte senza il suo sì.

### Chiusura

13. Riporta all'utente, in forma schematica:
    - il lotto eseguito, i componenti creati/modificati e l'esito della build
    - i test scritti e l'esito dell'esecuzione; i difetti e le regressioni, se presenti
    - la **checklist di collaudo** (colonna «Collaudo umano» del lotto) → verifica manuale facoltativa; lo stato del lotto è già `collaudato` per via dei test verdi.
