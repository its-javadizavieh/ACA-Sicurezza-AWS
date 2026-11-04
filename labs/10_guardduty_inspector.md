# Lab 10 - GuardDuty e Inspector

## Obiettivo
Analizzare segnalazioni senza generare attacchi e senza tentare l'attivazione di Inspector2 non autorizzata.

## Prerequisiti
Sessione Academy e `us-east-1`. Eventuali controlli dell'ambiente possono impedire l'attivazione del servizio.

## Scenario
Il gruppo deve distinguere una possibile attività malevola da tre debolezze software. I casi locali sono intenzionalmente sintetici e non rappresentano CVE reali.

## Step (numerati)

1. GuardDuty: Aprire GuardDuty -> Settings o Findings.
2. Verificare se esiste gia un detector.
3. Se esiste un detector, raccogliere solo evidenze disponibili in lettura.
4. Se non esiste, creare un detector solo se permesso dall'ambiente e dal budget del lab.
5. Se il detector e' stato creato da te, generare un sample finding.
6. Aprire Findings e filtrare per sample.
7. Annotare tipo, risorsa, gravita, regione e marcatura `SAMPLE`.
8. Inspector: Svolgere i casi Inspector localmente, senza attivare il servizio.
9. In `consegna10.md` creare sezione `Priorita vulnerabilita`.
10. Ordinare V1, V3, V2.
11. Aggiungere V4 come caso di copertura assente.
12. Per ogni caso indicare responsabile, intervento e prova di chiusura.

## Svolgimento guidato

Evidenza GuardDuty da raccogliere:

- verificare se esiste un detector in `us-east-1`;
- se un detector e' gia presente, raccogliere solo finding o sample disponibili in lettura;
- se il lab consente la creazione, creare un detector personale e generare un sample finding;
- annotare tipo, risorsa, gravita, regione e marcatura `SAMPLE`;
- il sample finding non e' un incidente reale.

Comandi opzionali GuardDuty, compatibili con Ubuntu, macOS e Windows PowerShell:

    aws guardduty list-detectors --region us-east-1

    aws guardduty create-detector --enable --region us-east-1
    
    aws guardduty create-sample-findings --detector-id DETECTOR_ID --finding-types Backdoor:EC2/DenialOfService.Tcp --region us-east-1

Priorita Inspector da scrivere in `consegna10.md`:

- V1: prima priorita, per esposizione, dati critici, gravita alta e patch disponibile;
- V3: seconda priorita, per esposizione e dati critici, anche senza patch immediata;
- V2: terza priorita, per contesto isolato e dati sintetici;
- V4: copertura assente, non prova di assenza vulnerabilita.

Controllo finale:

- ogni vulnerabilita deve avere responsabile, intervento e prova di chiusura;
- svolgere i casi Inspector localmente, senza attivare il servizio.

Cleanup da CLI solo per detector creato nel lab, compatibile con Ubuntu, macOS e Windows PowerShell:

    aws guardduty delete-detector --detector-id DETECTOR_ID --region us-east-1

## Output atteso
`consegna10.md`: scheda G1, priorità V1–V3, trattamento V4 e piano di verifica. Specifica percorso AWS o locale.

## Checkpoint
SAMPLE non è un incidente reale. Una vulnerabilità non prova sfruttamento. Inspector2 non diventa autorizzato per la sola presenza di `inspector:*`.

## Troubleshooting rapido
Detector già presente: sola lettura o caso locale. Attivazione negata: nessuna modifica di IAM. Esempio non visibile: controlla regione, detector e filtri, poi usa il caso fornito.

## Cleanup obbligatorio
Se e solo se hai creato un detector per il lab, elimina quel detector: `aws guardduty delete-detector --detector-id DETECTOR_ID --region us-east-1`; verifica con ListDetectors. L'eliminazione rimuove configurazione e finding del detector. Elimina estratti non anonimizzati e segnala eventuali costi o risorse residue.
