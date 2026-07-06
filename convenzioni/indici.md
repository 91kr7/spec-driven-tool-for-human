# Convenzione — Indici

Gli indici sono la mappa del codice: servono a localizzare i componenti senza esplorare i sorgenti. Il planner li legge; l'implementatore li scrive e li mantiene.

## Struttura a due livelli

```
.sdd/indici/
├── moduli.md          ← indice radice: un modulo per riga
├── utenti.md          ← indice di modulo: un componente per riga
└── ...
```

- Un **modulo** è un'area di dominio (es. utenti, catalogo, prestiti), non un layer tecnico: copre sia il lato backend sia il lato frontend della sua area.

## Formato dell'indice radice (`moduli.md`)

| Modulo | Responsabilità | Indice |
|--------|----------------|--------|
| utenti | Anagrafica degli utenti della biblioteca | `utenti.md` |

## Formato dell'indice di modulo (`<modulo>.md`)

| Componente | Tipo | Percorso | Responsabilità | Spec |
|------------|------|----------|----------------|------|
| PrestitoService | servizio backend | `backend/src/.../loan/PrestitoService.java` | Registra e chiude i prestiti applicando le regole di dominio | `../spec/prestiti/prestito-service.md` |

- La colonna **Tipo** classifica il componente: entity, servizio backend, endpoint REST, componente UI, ecc.
- Il percorso del file sorgente vive **solo qui**: la spec del componente non lo ripete.
- Una riga per componente, responsabilità in una frase: l'indice localizza, non spiega.

## Come si consultano (discesa a imbuto)

1. Leggi `moduli.md` e individua i moduli candidati.
2. Apri **solo** gli indici dei moduli candidati e individua i componenti.
3. Per capire un componente, apri la sua spec; il codice si apre solo come ultima verifica mirata.

## Come si mantengono

- Chi crea o modifica un componente aggiorna l'indice del suo modulo **nella stessa run**.
- Chi crea un modulo nuovo aggiunge la riga in `moduli.md` e crea il file indice, **nella stessa run**.
- Un indice che non rispecchia il codice è peggio di nessun indice: in caso di divergenza, segnalala e correggi l'indice.
