# Lab 10 - GuardDuty e Inspector

## Obiettivo

Esplorare GuardDuty, produrre evidenze analizzabili senza generare attacchi e trasformare i finding in azioni motivate.

## Prerequisiti

- Sessione AWS Academy attiva e regione `us-east-1`.
- AWS CLI configurata solo per i passaggi opzionali.
- Non modificare ruoli IAM e non attivare servizi o piani di protezione se l'ambiente non lo consente.
- I finding marcati `[SAMPLE]` sono esempi AWS con valori fittizi, non incidenti reali.

## Scenario

Se GuardDuty è stato appena aperto, i contatori possono essere tutti a zero. Il laboratorio produce finding GuardDuty di esempio senza richiedere attività malevole reali.

1. osservare lo stato reale di GuardDuty;
2. generare finding SAMPLE in GuardDuty;

## Parte A - Orientamento nella console

1. Verificare che la regione selezionata sia `us-east-1`.
2. Aprire **GuardDuty > Summary** e annotare: stato del servizio, totale finding, risorse con finding e data dell'ultimo aggiornamento.
3. Aprire **GuardDuty > Settings** e verificare se esiste un detector. Non modificare un detector condiviso.
4. Annotare il numero di finding GuardDuty e l'eventuale messaggio di servizio non attivo o `Access denied`.

## Parte B - GuardDuty con finding SAMPLE

### Percorso console

Usare questo percorso solo se il detector appartiene alla sessione del lab e l'azione è consentita.

1. In **GuardDuty > Settings**, individuare la sezione **Sample findings**.
2. Selezionare **Generate sample findings**. La console genera esempi per i tipi supportati.
3. Aprire **Findings** e cercare il prefisso `[SAMPLE]`.
4. Aprire un finding con gravità alta e uno con gravità media o bassa.
5. Per ciascuno compilare la scheda seguente in `consegna10.md`:

| Campo                                       | Finding G1 | Finding G2 |
| ------------------------------------------- | ---------- | ---------- |
| Titolo e tipo                               |            |            |
| Gravità                                     |            |            |
| Risorsa interessata                         |            |            |
| Regione                                     |            |            |
| Attività descritta                          |            |            |
| Evidenza che è SAMPLE                       |            |            |
| Prima verifica da eseguire in un caso reale |            |            |
| Possibile contenimento                      |            |            |

6. Aprire i dettagli JSON di G1 e individuare almeno `type`, `severity`, `resource` e l'indicatore `sample`.
7. Confrontare G1 e G2: decidere quale analizzare per primo e motivare la scelta in due frasi.

### Percorso CLI opzionale

Elencare i detector:

```bash
aws guardduty list-detectors --region us-east-1
```

Solo se non esiste un detector e l'ambiente consente la creazione e gli eventuali costi:

```bash
aws guardduty create-detector --enable --region us-east-1
```

Generare due tipi specifici sostituendo `DETECTOR_ID`:

```bash
aws guardduty create-sample-findings \
  --detector-id DETECTOR_ID \
  --finding-types Backdoor:EC2/DenialOfService.Tcp UnauthorizedAccess:EC2/SSHBruteForce \
  --region us-east-1
```

Se la console o la CLI nega l'operazione, registrare l'errore senza modificare IAM.

## Output atteso

Creare `consegna10.md` con:

1. stato iniziale di GuardDuty;
2. schede G1 e G2, oppure motivazione documentata dell'impossibilità di generarle;
3. confronto motivato tra G1 e G2;
4. distinzione esplicita tra dato reale osservato e finding SAMPLE.

## Checkpoint

- `[SAMPLE]` non è un incidente reale.
- Zero finding non significa automaticamente zero vulnerabilità.

## Troubleshooting rapido

- **Summary tutto a zero:** verificare regione, stato del detector e data di aggiornamento; poi generare sample.
- **Sample non visibili:** aprire Findings, rimuovere filtri incompatibili e cercare `[SAMPLE]`.
- **Detector condiviso:** sola lettura; non eliminarlo e non modificarne le impostazioni.
- **Access denied:** registrare servizio, pagina e azione negata; non modificare IAM.

## Cleanup obbligatorio

- Archiviare i sample se il detector deve essere mantenuto e l'account consente l'operazione.
- Eliminare il detector solo se è stato creato personalmente durante questo lab:

```bash
aws guardduty delete-detector --detector-id DETECTOR_ID --region us-east-1
```

- Verificare il risultato con `list-detectors`.
- Non eliminare detector o configurazioni condivise.
- Eliminare eventuali estratti contenenti identificativi non anonimizzati e segnalare costi o risorse residue.

## Riferimenti AWS

- [Generazione di finding SAMPLE in GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/sample_findings.html)
