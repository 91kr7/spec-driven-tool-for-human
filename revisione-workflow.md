# Revisione del workflow — buchi e incongruenze

- Data → 2026-07-07
- Stato analizzato → v0.0.8, commit `635859b`, working tree pulito
- Ambito → 4 comandi, 5 agent, 4 convenzioni, manifest
- Metodo → matrice chi-legge/chi-scrive di ogni artefatto + simulazione dei cicli di vita (piano nuovo, evolutiva, giro di correzioni, morte di sessione, regressioni)
- Uso del file → checklist per pianificare le correzioni; spuntare i punti decisi

## Checklist di pianificazione

### 🔴 Buchi veri

- [ ] 1. Deadlock sull'evolutiva che cambia un comportamento già testato
- [ ] 2. La colonna «Collaudo umano» non arriva mai al tester
- [ ] 3. Assunzioni e deroghe del piano non arrivano mai al developer
- [ ] 4. `.archi` nasce all'init e nessuno lo aggiorna più
- [ ] 5. Il tester non sa quali componenti ha creato il lotto

### 🟡 Incongruenze di processo

- [ ] 6. Giro di correzione post-referto: stato, verifica meccanica e tester nuovo
- [ ] 7. Il referto del tester non è persistito su file
- [ ] 8. `/sdd-plan` non è rientrante dopo una morte di sessione
- [ ] 9. Ambiguità: la «run globale» include gli e2e?
- [ ] 10. Gate d'ingresso di `/sdd-dev`: residui multipli senza priorità
- [ ] 11. La configurazione di test è un componente senza casa
- [ ] 12. La scala di localizzazione è prescritta solo per le modifiche
- [ ] 13. Test dei REQ cablati sull'«API REST del backend»

### 🔵 Osservazioni — scelte da confermare

- [ ] 14. Regressione nota + stop: il lotto precedente resta `collaudato`
- [ ] 15. Due run globali certe per lotto + due potenziali dell'orchestratore
- [ ] 16. Il planner scrive `bozza→validato`: eccezione non dichiarata
- [ ] 17. Le deroghe segnalano «la spec va corretta» ma nessun passo la corregge
- [ ] 18. Adozione di codebase esistenti non supportata
- [ ] 19. Flusso bug progettato ma non implementato
- [ ] 20. Cosmetiche: description del plugin e degli agent non aggiornate

---

## 🔴 Buchi veri

### 1. Deadlock sull'evolutiva che cambia un comportamento già testato

Il punto più grave: blocca il flusso, non solo lo degrada.

- Scenario → il lotto 2 di un piano vecchio chiude «il prestito dura 30 giorni» e il tester scrive un test che lo asserisce; un'evolutiva validata introduce «la durata diventa configurabile»; il developer del nuovo lotto implementa e il vecchio test diventa rosso **legittimamente**.
- `agents/sdd-developer.md:50` → «Se un tuo intervento rompe un test, correggi il **tuo codice**, mai il test» — regola senza eccezioni.
- `agents/sdd-developer.md:51` → «Il lotto è finito **solo** con build verde e run globale verde».
- `commands/sdd-dev.md:39-40` → la verifica meccanica pretende «test preesistenti verdi» e rimanda indietro il developer «finché la verifica non passa».
- Conseguenza → il developer non può correggere il test, non può consegnare col rosso; il tester — l'unico autorizzato a correggere un test superato (`agents/sdd-tester.md:66`) — arriva solo **dopo** una consegna che non può avvenire.
- Le domande all'umano non salvano → l'umano può confermare che il test è superato, ma il developer resta senza il potere di agire.
- Direzione di fix (coerente con la separazione dei poteri) → il developer dichiara i rossi da «contratto superato» nell'output, citando il REQ del piano corrente che cambia il contratto; la verifica meccanica accetta i rossi solo se dichiarati e motivati; il tester li sistema in fase test, dove ha già il mandato.

### 2. La colonna «Collaudo umano» non arriva mai al tester

- `agents/sdd-tester.md:48` → gli e2e si derivano «dalle spec dei componenti UI e dalla colonna «Collaudo umano» del lotto».
- La colonna vive solo nella tabella di `lotti.md` (`agents/sdd-planner.md:75`).
- Il tester riceve soltanto file del lotto, `requirements.md` e data (`commands/sdd-dev.md:45-48`); il frontmatter del file di lotto ha `lotto/feature/req_chiusi/dipende`, senza collaudo (`agents/sdd-planner.md:85`).
- Conseguenza → il tester cita una sorgente che non può raggiungere.
- Direzione di fix → il planner replica la voce «Collaudo umano» nel file del lotto; coerente col principio «deve bastare da solo» e col pattern già accettato per `req_chiusi`.

### 3. Assunzioni e deroghe del piano non arrivano mai al developer

- Le sezioni «Assunzioni/decisioni» e «Deroghe» vivono solo in `lotti.md` (`agents/sdd-planner.md:79-80`).
- Il developer non legge `lotti.md`: il suo Passo 0 copre `.archi`, file del lotto, REQ chiusi e indici (`agents/sdd-developer.md:28-33`).
- Scenario → in validazione è stato deciso «durata hardcodata in deroga alla spec» o «migrazioni con Flyway»; chi implementa non lo vede e può decidere diversamente, o richiedere all'umano ciò che è già stato deciso.
- Viola il principio «il file del lotto deve bastare da solo» (`agents/sdd-planner.md:87`).
- Direzione di fix → il planner riporta nel file di ogni lotto le assunzioni e le deroghe che riguardano quel lotto.

### 4. `.archi` nasce all'init e nessuno lo aggiorna più

- Il tester installa Playwright e configura `webServer` (`agents/sdd-tester.md:57-58`): da quel momento esiste una suite e2e con un comando che `.archi` non registra.
- Il developer aggiunge dipendenze nei lotti: `.archi` (sezione Dipendenze) resta fermo all'init.
- Chi si affida a `.archi` → l'orchestratore («in dubbio, rilancia i comandi indicati in `.sdd/.archi`», `commands/sdd-dev.md:39` e `commands/sdd-dev.md:51`) e ogni agent futuro (Passo 0 di planner, developer, tester).
- Conseguenza → le certificazioni future girano con una suite parziale e con comandi stantii.
- Direzione di fix → regola «chi cambia infrastruttura (comandi, dipendenze) aggiorna `.archi` nella stessa run», sul modello della manutenzione degli indici; vale per developer e tester → 2 agent → per CLAUDE.md è una convenzione da centralizzare.

### 5. Il tester non sa quali componenti ha creato il lotto

- Per design gli INT `crea` non nominano file né classi (`agents/sdd-planner.md:68`): i nomi reali li decide il developer e finiscono in indici e spec.
- Il tester deve derivare gli unit test «dalle spec dei componenti coinvolti» (`agents/sdd-tester.md:34`), ma l'indice del modulo non distingue le righe nuove dalle preesistenti.
- L'unico filtro possibile (aprire tutte le spec del modulo e guardare i «Requisiti serviti») non è scritto da nessuna parte e costa letture.
- L'informazione esiste già gratis → l'output del developer elenca «componenti creati o modificati, con i percorsi» (`agents/sdd-developer.md:70`) e l'orchestratore ce l'ha in mano al passo 8, ma al passo 10 non la passa al tester (`commands/sdd-dev.md:45-48`).
- Direzione di fix → aggiungere l'elenco dei componenti toccati agli input del tester nel passo 10 di `/sdd-dev`.

---

## 🟡 Incongruenze di processo

### 6. Giro di correzione post-referto: tre smagliature in un passo

Tutte in `commands/sdd-dev.md:52`, quando l'utente manda le correzioni al developer:

- Stato → resta `implementato` durante la rilavorazione (il design prevedeva il ritorno a `in corso`); se la sessione muore a metà correzione, la run successiva salta dritta in fase test su codice potenzialmente a build rotta.
- Verifica meccanica → non viene ripetuta dopo le correzioni: si va a «poi ripeti la fase Test» senza ricontrollare build verde e indici.
- Tester → «ripeti la fase Test» rimanda al passo 10 = **nuovo Task** `sdd-tester`, mentre per il developer si usa il resume; il tester nuovo rideriva tutto il piano dei test da zero, quando un `SendMessage` al tester esistente («il developer ha corretto X e Y, riesegui») costerebbe una frazione.

### 7. Il referto del tester non è persistito su file

- Se l'utente sceglie «fermarsi qui», difetti, triage e regressioni vivono solo in chat; il principio di progetto è «stato durevole su file, la sessione è usa-e-getta».
- Il sistema si auto-ripara (i test sono su disco: rieseguirli ritrova i difetti), ma la run successiva paga un tester che ricontestualizza da zero.
- Nessuna istruzione dice al tester di **riusare** i test già scritti per il lotto: il Passo 0 gli fa leggere la struttura dei test solo «per inserirsi in modo idiomatico» (`agents/sdd-tester.md:35`), quindi può riscriverli accanto.
- Direzione di fix (due pezzi piccoli) → referto su file (es. nella cartella del piano) e/o una riga al tester: «se trovi test già scritti per i REQ del lotto, riusali e completali».

### 8. `/sdd-plan` non è rientrante dopo una morte di sessione

- Scenario → la sessione muore tra i due gate: `requirements.md` è già `validato`, i lotti no.
- Rilanciare il comando fa ripartire il planner dal Passo 1: «Estrai le feature... Scrivi requirements.md» (`agents/sdd-planner.md:31-32`) → riscrive un file già validato e può **rinumerare i REQ**.
- La rinumerazione è vietata dalla convenzione (`convenzioni/identificatori.md:19`: «mai rinumerare dopo la validazione»).
- Non è errore umano → è la stessa ripartenza a freddo che `/sdd-dev` gestisce esplicitamente con gli stati.
- Direzione di fix → il planner (o il comando) controlla il frontmatter: `requirements.md` con `stato: validato` → si riparte dal Passo 3 senza rigenerare.

### 9. Ambiguità: la «run globale» include gli e2e?

- `convenzioni/esecuzione-comandi.md:19-23` parla di «intera suite» senza pronunciarsi sugli e2e, che richiedono ambiente avviato e costano molto.
- Il developer alla non-regressione (`agents/sdd-developer.md:50`) deve avviare frontend e backend per Playwright? Due agent interpreteranno in due modi.
- Direzione di fix (proposta economica) → developer: run globale = unit + API; tester: run globale = tutto, e2e compresi (il verdetto finale). Risultato: una sola esecuzione e2e per lotto.

### 10. Gate d'ingresso di `/sdd-dev`: residui multipli senza priorità

- `commands/sdd-dev.md:21-24` tratta i tre casi (`testato`, `implementato`, `in corso`) come alternative singole.
- Scenario → coesistono un lotto `implementato` e un lotto `in corso` (due run interrotte in momenti diversi): l'ordine dei bullet può far saltare in fase test ignorando l'anomalia `in corso`, che invece dovrebbe fermare tutto.
- Direzione di fix → una riga: «`in corso` vince su tutto: se esiste, fermati a prescindere dagli altri stati».

### 11. La configurazione di test è un componente senza casa

Tre regole insieme sono incoerenti:

- L'appendice del developer classifica «configurazione» come tipo di componente con spec (`agents/sdd-developer.md:166-171`).
- La convenzione indici dice «chi crea un componente aggiorna l'indice nella stessa run» (`convenzioni/indici.md:47-48`).
- Il tester crea `playwright.config.ts` con il divieto esplicito di toccare spec e indici (`agents/sdd-tester.md:75`).
- Direzione di fix → dichiarare (nel tester o nella convenzione indici) che l'infrastruttura di test è fuori dal perimetro di indici e spec.

### 12. La scala di localizzazione è prescritta solo per le modifiche

- `agents/sdd-planner.md:48` → «Per gli interventi in **modifica** → localizza i punti salendo la scala»: letteralmente, la scala si usa quando sai già che è una modifica.
- Ma per decidere `tipo: crea` bisogna aver verificato negli indici che il punto **non** esiste: il testo può indurre a classificare `crea` senza guardare, pianificando duplicati.
- Mitigato a valle da «la realtà vince sul piano» del developer (`agents/sdd-developer.md:16`), però il piano validato dall'umano risulterebbe sbagliato in partenza.
- Direzione di fix → una riga: «la classificazione crea/modifica si decide consultando gli indici (livello 1), sempre».

### 13. Test dei REQ cablati sull'«API REST del backend»

- `agents/sdd-tester.md:46` assume uno stack web con backend REST.
- Su una CLI, una libreria o un batch il tester non ha una regola di ripiego.
- Direzione di fix → o dichiarare il perimetro del plugin («web app»), o generalizzare: «al confine pubblico più esterno non-GUI dello stack (API REST se c'è un backend, API pubblica se è una libreria, invocazione del comando se è una CLI)».

---

## 🔵 Osservazioni — scelte da confermare, non difetti

### 14. Regressione nota + stop: il lotto precedente resta `collaudato`

- Se il tester marca una regressione e l'utente sceglie «fermarsi qui», il lotto rotto resta `collaudato` in tabella benché il suo contratto sia violato nel codice.
- L'invariante però si ripristina da solo: nessun nuovo `collaudato` è possibile senza run globale verde, quindi la violazione non sopravvive a una certificazione.
- Da confermare → la finestra di incoerenza della tabella è accettata?

### 15. Due run globali certe per lotto + due potenziali dell'orchestratore

- Run certe → il developer prima di consegnare, il tester per certificare: costo strutturale motivato (barriere diverse).
- Run potenziali → i «in dubbio, rilancia tu» dell'orchestratore (`commands/sdd-dev.md:39` e `commands/sdd-dev.md:51`) sono una terza e quarta esecuzione possibile.
- Da confermare → tenere i rilanci dell'orchestratore o fidarsi dei report (che già riportano comandi ed esiti)?

### 16. Il planner scrive `bozza→validato`: eccezione non dichiarata

- `agents/sdd-planner.md:41` e `agents/sdd-planner.md:94` → è il planner (subagent) a impostare `stato: validato` nei frontmatter.
- La regola generale dice «gli stati li scrivono solo gli orchestratori» (`commands/sdd-dev.md:30`; per gli stati di lotto `agents/sdd-planner.md:77`).
- Difendibile (avviene solo dopo conferma umana via resume), ma è un'eccezione non dichiarata.
- Da confermare → dichiararla con una riga, o spostare la scrittura dello stato sull'orchestratore `/sdd-plan`?

### 17. Le deroghe segnalano «la spec va corretta» ma nessun passo la corregge

- `agents/sdd-planner.md:80` → la deroga registra che la spec di business va corretta.
- Nessun passo del workflow la corregge: il cerchio si chiude solo se l'umano rilancia `/sdd-analyse` in correzione.
- Coerente col «for-human», ma il piano segnala un debito che nessuno riscuote.
- Da confermare → basta così, o l'output finale di `/sdd-plan` deve ricordare esplicitamente all'utente le correzioni di spec pendenti?

### 18. Adozione di codebase esistenti non supportata

- `sdd-init` crea solo da zero e ha il divieto di ispezionare i file (`agents/sdd-architect.md:15` e `agents/sdd-architect.md:62`).
- Planner, developer e tester assumono `.archi` e indici: un progetto legacy non ha porta d'ingresso nel workflow.
- Da confermare → perimetro voluto (solo greenfield) o pezzo mancante (un futuro «sdd-adopt» che fotografa l'esistente)?

### 19. Flusso bug progettato ma non implementato

- Il design esiste (triage: bug di codice / bug di spec / falso bug → evolutiva; verbale atteso/osservato/riproduzione; un lotto = un bug; test di regressione come definition of done).
- Il planner attuale non lo contiene: lavoro noto in sospeso, registrato per completezza.

### 20. Cosmetiche

- La description di `.claude-plugin/plugin.json` e `.claude-plugin/marketplace.json` è ferma a «dalla richiesta all'analisi di business»: il plugin ora copre l'intero ciclo (analisi → init → piano → sviluppo e test).
- Le description degli agent dicono «ragionamento esteso» anche dopo il downgrade dell'effort a `high`.

---

## Sintesi e priorità

- L'impianto regge → separazione dei poteri, stati su file, economia dei token e rientranza di `/sdd-dev` sono coerenti e si autoriparano nei casi simulati.
- Quattro dei cinque buchi sono della stessa famiglia → informazione che esiste ma non raggiunge chi ne ha bisogno (collaudo → tester, deroghe → developer, componenti creati → tester, comandi nuovi → `.archi`).
- Il punto 1 è l'unico bloccante di flusso → alla prima evolutiva che cambia comportamento testato, il developer resta incastrato tra due regole assolute.
- Priorità consigliata → correggere il punto 1 prima della prova end-to-end sulla biblioteca, che ha già un candidato naturale per innescarlo (durata del prestito configurabile).
