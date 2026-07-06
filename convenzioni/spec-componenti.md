# Convenzione — Spec dei componenti

La spec di un componente è il suo **contratto**: descrive cosa fa e quali regole rispetta, mai come è fatto dentro. È la fonte da cui si derivano gli unit test e da cui le fasi future capiscono il componente senza aprire il codice.

## Posizione e nome

- Percorso → `.sdd/spec/<modulo>/<componente>.md` (nome file in kebab-case, es. `prestito-service.md`).
- Il percorso del file sorgente NON si scrive nella spec: vive nell'indice del modulo (vedi convenzione indici).

## Struttura del file

```markdown
---
modulo: <modulo>
componente: <NomeComponente>
tipo: <entity | servizio backend | endpoint REST | componente UI | ...>
---

# <NomeComponente>

**Scopo** → una o due righe: a cosa serve il componente.

## API

- `<firma o operazione>` → cosa fa, quando rifiuta (con quale errore), quali effetti produce.

## Regole e invarianti

- Una riga per regola: condizioni che devono essere sempre vere, comprese quelle garantite a livello di persistenza.

## Dipendenze

- Gli altri componenti usati, citati per nome (con il modulo, se diverso).

## Requisiti serviti

- Gli id qualificati dei requisiti, es. `plan-<slug>/REQ-15` (convenzione identificatori).
```

Le sezioni senza contenuto si omettono (es. una entity può non avere «API»).

## La quota giusta: contratto, non implementazione

- La spec descrive il **comportamento osservabile**: cosa entra, cosa esce, quando rifiuta, quali invarianti valgono.
- Vietati: corpi dei metodi, dettagli privati, strutture interne, chiamate al framework.
- Test pratico → la spec cambia **solo se cambia il comportamento osservabile**; se un refactor interno ti costringe a toccarla, era scritta troppo bassa.

## Pseudocodice: ammesso, con un confine

- Ammesso quando una regola è troppo complessa per la prosa: logica a più rami, formule, macchine a stati, algoritmi di assegnazione.
- Deve restare a quota contratto → descrive il **risultato** che qualunque implementazione deve produrre, non i passi interni del codice.
- Test pratico → se due implementazioni diverse devono entrambe rispettarlo, è contratto (dentro); se descrive come il codice ci arriva, è implementazione (fuori).
- Ogni ramo dello pseudocodice = un caso di test.

## Chi la scrive e quando

- La scrive l'**implementatore**, all'inizio del lavoro sul componente (contract-first: prima la spec, poi il codice).
- Chi modifica il comportamento di un componente aggiorna la sua spec **nella stessa run**.
