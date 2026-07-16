# Convenzione — Commenti nel codice

Il codice si spiega da sé con nomi chiari; il commento è l'eccezione, non l'abitudine.

## Regola generale

- Vietati i commenti che narrano cosa fa il codice riga per riga o passo per passo.
- Ammesso un commento solo se comunica qualcosa che il codice da solo non può esprimere (es. il perché di una scelta non ovvia).
- Se un nome più chiaro rende il commento superfluo, si rinomina invece di commentare.

## Applicazione ai test

- Ogni test dichiara in **un solo commento** (una riga, sopra il test) il requisito o la regola che verifica (id qualificato).
- Nessun altro commento nel corpo del test: nomi e asserzioni devono bastare da soli.
