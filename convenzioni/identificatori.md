# Convenzione — Identificatori

Schema degli id negli artefatti del workflow (requisiti, interventi).

## Schema

- `REQ-n` → requisito; progressivo nel piano; il testo vive solo in `requirements.md`
- `INT-n` → intervento; progressivo nel lotto; definito solo nel file del proprio lotto

## Spazio dei nomi = percorso

- Gli id sono **locali al file che li definisce** → è il percorso a renderli unici
- Dentro il proprio ambito → id nudo (`REQ-3`, `INT-2`)
- Fuori dal proprio ambito → id qualificato col percorso → `plan-<slug>/REQ-3`, `lotto-<slug>/INT-2`
- Esempio → un test derivato da un requisito cita `plan-gestione_biblioteca/REQ-3`

## Regole

- Id stabili → mai rinumerare dopo la validazione
- Dipendenze tra INT → solo dentro lo stesso lotto; tra lotti si dichiara la dipendenza a livello di lotto (in `lotti.md`)
