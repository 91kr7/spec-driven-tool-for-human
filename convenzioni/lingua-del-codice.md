# Convenzione — Lingua del codice

Tutto ciò che finisce nel progetto generato è **rigorosamente in inglese**; fanno eccezione solo i testi della GUI visibili all'utente finale, la cui lingua la decide l'umano.

## In inglese, sempre

- Codice sorgente → nomi di classi, funzioni, variabili, costanti, tipi.
- Commenti nel codice, javadoc e docstring.
- Struttura del progetto → nomi di file, cartelle, moduli e package, compreso lo scheletro creato in fase di init.
- Codice dei test → nomi dei test, asserzioni, commenti.
- Testi tecnici non destinati all'utente finale → log, messaggi di errore interni, chiavi di API e JSON, nomi di rotte e parametri.

## Nella lingua decisa dall'umano

- Solo i testi della GUI visibili all'utente finale → etichette, titoli, messaggi, conferme.
- La lingua della GUI la comunica l'umano; se non è indicata da nessuna parte (prompt, spec, `.archi`), è una **domanda per l'umano** → convenzione intermediazione-domande.

## Fuori dal perimetro di questa convenzione

- Gli artefatti del workflow (`.sdd/`: analisi, piani, spec dei componenti, indici, `.archi`) restano in italiano, come previsto dal CLAUDE.md di progetto.
