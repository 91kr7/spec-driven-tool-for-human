# Convenzione — Intermediazione domande/risposte

Come un subagent (che gira non presidiato) chiede delucidazioni all'umano: l'orchestratore fa da intermediario, e il contesto del subagent non va perso.

## Ruolo del subagent

- Raccogli le domande a cui solo l'umano può rispondere e restituiscile all'orchestratore; non scriverle nel file di output. Poi fermati.
- Quando vieni ripreso con le risposte, mantieni il contesto del giro precedente: non ripartire da zero.
- Nel file di output non lasciare segnaposto (niente `<...>`): ogni punto o è deciso, o è un'assunzione esplicita con un default motivato.
- Se l'umano non vuole decidere un punto, registralo come assunzione esplicita, mai come domanda nel file.
- Se non hai domande, prosegui senza fermarti.

## Ruolo dell'orchestratore (comando)

- Quando il subagent restituisce domande per l'umano, ponile all'utente (l'intermediario sei tu) e raccogli le risposte.
- Riprendi **lo stesso subagent** con `SendMessage` (non un nuovo Task) passandogli le risposte: così mantiene il contesto del primo giro.
- Ripeti finché non restano domande.
