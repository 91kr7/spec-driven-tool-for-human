# Convenzione — Identificatori

Schema degli id usati negli artefatti del workflow (requisiti, interventi).

## Schema

- `REQ-n` → identifica un requisito. Numerazione progressiva nel piano; il testo del requisito vive solo in `requirements.md`.
- `INT-n` → identifica un intervento. Numerazione progressiva nel lotto; definito solo nel file del proprio lotto.

## Spazio dei nomi = percorso

- Gli id sono **locali al file che li definisce**: è il percorso del file a renderli unici.
- Dentro il proprio ambito si usa l'id nudo: `REQ-3`, `INT-2`.
- Fuori dal proprio ambito l'id va qualificato con il percorso: `plan-<slug>/REQ-3`, `lotto-<slug>/INT-2`.
- Esempio: un test derivato da un requisito lo cita come `plan-gestione_biblioteca/REQ-3`.

## Regole

- Gli id sono stabili: mai rinumerare dopo la validazione.
- Le dipendenze tra INT si dichiarano solo dentro lo stesso lotto; tra lotti la dipendenza si dichiara a livello di lotto, in `lotti.md`.
