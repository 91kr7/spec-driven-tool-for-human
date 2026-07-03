# Convenzione — Intermediazione domande/risposte

Pattern per far chiedere delucidazioni all'umano quando un subagent (non presidiato) ha bisogno di risposte. L'orchestratore fa da intermediario e il contesto del subagent non va perso.

## Ruolo del subagent

- Raccogli le domande a cui serve l'umano → **restituiscile all'orchestratore**, non scriverle nel file di output. Poi fermati.
- Alla ripresa (arrivano le risposte) → **mantieni il contesto** del giro precedente, non ripartire da zero.
- **Nessun segnaposto nel file** (niente `<...>`) → ogni punto è deciso o è un'**assunzione esplicita** con default motivato.
- Punto che l'umano non vuole decidere → assunzione esplicita, mai domanda nel file.
- Nessuna domanda → prosegui senza fermarti.

## Ruolo dell'orchestratore (comando)

- Il subagent restituisce **domande per l'umano** → ponile all'utente (sei tu l'intermediario) e raccogli le risposte.
- **Riprendi lo stesso subagent** con `SendMessage` (NON un nuovo Task) passando le risposte → mantiene il contesto del primo giro.
- Ripeti finché non restano domande.
