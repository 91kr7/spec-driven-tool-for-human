# CLAUDE.md — Linee guida di progetto

Regole vincolanti per chiunque (umano o agent) lavori in questo repository.

## 1. Lingua

- File Markdown → **italiano**
- Conversazione in chat → **italiano**
- Vietati i calchi dall'inglese (traduzioni parola per parola)
- Terminologia tecnica consolidata (es. `spec`, `plugin`, `commit`, `hook`) → ammessa in originale, non forzare la traduzione

## 2. Stile dei Markdown

- Struttura gerarchica e chiara (titoli e sotto-titoli)
- Formato schematico → elenchi, tabelle, checklist
- Prosa ridotta al minimo indispensabile
- Regola pratica: una riga = una informazione

## 3. Convenzioni

**Cos'è una convenzione** → una regola che **più agent** devono conoscere.

- Regola condivisa da 2+ agent → **convenzione centralizzata** (scritta una volta sola)
- Regola usata da un solo agent → resta nel suo prompt, non è una convenzione
- Gli agent la **richiamano**, non la ricopiano → fonte unica, zero duplicazione

**Come si organizzano**

- Niente file unico "tuttofare"
- **Una convenzione = un file dedicato**
- File piccoli, a responsabilità singola
- Obiettivo → ogni agent riceve solo il contesto che gli serve

**Duplicazione → centralizzare**

- Se una regola già descritta altrove viene ripetuta/riscritta → va estratta e centralizzata
- Trigger → **seconda occorrenza**: la 2ª volta che una regola serve, si centralizza
- Prima occorrenza → può restare locale; dalla seconda → fonte unica richiamata da tutti
- Nessuna regola condivisa vive duplicata in due punti

## 4. Modifiche ai file

- **Diff minimo** → applicare la modifica più piccola possibile
- Toccare solo ciò che serve, non riscrivere parti già valide
- Preferire modifiche mirate a riscritture complete
- Vantaggi → meno rischio, revisioni più semplici

## 5. Commit

- Messaggi in **italiano**
- **Non** aggiungere il nome dell'assistente (nessun `Co-Authored-By`)
- Messaggio schematico → oggetto sintetico + eventuali dettagli in elenco
