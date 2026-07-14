---
description: Implementa e testa un intero piano tecnico, lotto per lotto: per ogni lotto sviluppo (spec, codice, indici) e subito dopo i test derivati dai contratti; a test verdi il lotto è certificato in automatico (collaudato), senza gate umano, e si passa al lotto successivo. Delega ai subagent sdd-developer e sdd-tester.
argument-hint: "<percorso della cartella del piano, es. .sdd/plans/plan-gestione_biblioteca>"
---

# /sdd-dev — Sviluppo e test di un piano, lotto per lotto

Esegue **tutti i lotti** del piano indicato in `$ARGUMENTS`, uno dopo l'altro: per ciascuno prima lo sviluppo (subagent `sdd-developer`), poi **subito** i test (subagent `sdd-tester`). Certificato un lotto, passa al successivo, finché il piano è completo o resta bloccato.

## Ruolo

- Tu (la sessione principale) sei l'**orchestratore**: non implementi e non scrivi test.
- Lo sviluppo lo fa `sdd-developer`; i test li scrive ed esegue `sdd-tester`. Sono due subagent distinti di proposito: chi certifica non è chi ha scritto il codice.
- Tu gestisci: il ciclo sui lotti e la scelta di ciascuno, gli stati su `lotti.md`, i gate d'ingresso e sui test rossi, le verifiche meccaniche e l'intermediazione delle domande.

## Passi

### Avvio (una volta sola)

1. Leggi `lotti.md` nella cartella del piano. Se il frontmatter riporta `stato: bozza`, fermati: il piano non è ancora validato dall'umano.
2. Gate d'ingresso:
   - un lotto è in stato `testato` (residuo di una run precedente alla certificazione automatica) → i suoi test erano verdi: portalo a `collaudato` e prosegui.
   - un lotto è in stato `implementato` → sviluppo concluso ma test mancanti: salta direttamente alla fase Test per quel lotto (stabilisci comunque se è l'ultimo lotto, passo 3, per la run globale del tester).
   - un lotto è in stato `in corso` → una run precedente si è interrotta: segnalalo all'utente e fermati, decide lui come procedere.

### Ciclo sui lotti — ripeti finché ci sono lotti lavorabili

3. Scegli il lotto → il primo in stato `da fare` con tutte le dipendenze in stato `collaudato`. Se non ce n'è nessuno, esci dal ciclo e vai alla **Chiusura del piano**. Stabilisci inoltre se è l'**ultimo lotto** del piano → lo è quando, oltre a quello scelto, nessun altro lotto resta da lavorare (tutti gli altri già `collaudato`): serve a decidere se far girare la **run globale** della suite nella fase Test (passo 10).
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
   - il subagent riporta build **verde**; lo sviluppo **non esegue i test** — la verifica e la non-regressione sono compito della fase Test. In dubbio, rilancia tu la build coi comandi canonici indicati in `.sdd/.archi`.
   - Se manca qualcosa, riprendi lo stesso subagent con l'elenco preciso delle mancanze, finché la verifica non passa.
9. Porta lo stato del lotto a `implementato`.

### Fase test (subito dopo lo sviluppo)

10. Lancia il subagent `sdd-tester` via Task, passandogli:
    - il percorso del file del lotto (`lotti/lotto-<slug>.md`)
    - il percorso di `requirements.md`
    - la data corrente
    - se è l'**ultimo lotto** del piano (passo 3) → su di esso va eseguita la run globale della suite; sui lotti intermedi solo i test del lotto
11. Domande del tester → stessa convenzione di intermediazione del passo 7.
12. Al rientro, valuta il referto:
    - **tutti i test verdi** → porta lo stato del lotto direttamente a `collaudato`: i test verdi certificano il lotto, senza gate di collaudo umano (in dubbio, rilancia i comandi di test indicati in `.sdd/.archi` per conferma).
    - **test rossi per difetti del codice** → presenta il referto all'utente e chiedigli come procedere: mandare le correzioni al developer (riprendi `sdd-developer` con l'elenco dei difetti, poi ripeti la fase Test) oppure fermarsi qui (lo stato resta `implementato`). Nessun giro di correzione parte senza il suo sì.

### Chiusura del lotto e passaggio al successivo

13. Riporta all'utente, in forma schematica, l'esito del lotto appena chiuso:
    - il lotto eseguito, i componenti creati/modificati e l'esito della build
    - i test scritti e l'esito dell'esecuzione; i difetti e le regressioni, se presenti
    - la **checklist di collaudo** (colonna «Collaudo umano» del lotto) → verifica manuale facoltativa; lo stato del lotto è già `collaudato` per via dei test verdi.
14. Torna al passo 3 per il lotto successivo.

### Chiusura del piano

15. Quando il passo 3 non trova più lotti lavorabili, riepiloga all'utente: i lotti certificati nella run e i componenti principali toccati, gli eventuali lotti rimasti indietro con il motivo, lo stato complessivo → **completo** se tutti i lotti sono `collaudato`, altrimenti **bloccato** con lo stato della tabella.
