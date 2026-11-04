# Lab 10 - GuardDuty e Inspector

## Obiettivo

Esplorare per la prima volta GuardDuty e Inspector, produrre evidenze analizzabili senza generare attacchi e trasformare finding e lacune di copertura in azioni motivate.

## Prerequisiti

- Sessione AWS Academy attiva e regione `us-east-1`.
- AWS CLI configurata solo per i passaggi opzionali.
- Non modificare ruoli IAM e non attivare servizi o piani di protezione se l'ambiente non lo consente.
- I finding marcati `[SAMPLE]` sono esempi AWS con valori fittizi, non incidenti reali.

## Scenario

Se GuardDuty o Inspector sono stati appena aperti, i contatori possono essere tutti a zero. Lo zero non dimostra che l'ambiente sia sicuro: può indicare assenza di eventi, servizio non attivo, regione errata o nessuna risorsa coperta. Il laboratorio separa quindi tre attività:

1. osservare lo stato reale dei servizi;
2. generare finding SAMPLE in GuardDuty, quando consentito;
3. analizzare casi Inspector realistici quando non esistono risorse scansionabili.

## Parte A - Orientamento nella console

1. Verificare che la regione selezionata sia `us-east-1`.
2. Aprire **GuardDuty > Summary** e annotare: stato del servizio, totale finding, risorse con finding e data dell'ultimo aggiornamento.
3. Aprire **GuardDuty > Settings** e verificare se esiste un detector. Non modificare un detector condiviso.
4. Aprire **Inspector > Dashboard**, **Findings** e **Account management/Coverage**, se accessibili.
5. Annotare separatamente:
   - numero di finding;
   - numero di risorse coperte;
   - tipi di scansione attivi o non attivi;
   - eventuali messaggi `Access denied`, servizio non attivo o nessuna risorsa idonea.

Checkpoint: `0 finding` e `0 risorse coperte` sono informazioni diverse e non devono essere interpretate allo stesso modo.

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

Solo se non esiste un detector e il docente autorizza creazione e costi:

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

Se la console o la CLI nega l'operazione, registrare l'errore senza modificare IAM e passare alla Parte C.

## Parte C - Inspector: stato reale e copertura

Non attivare Inspector2 nel Learner Lab se non è esplicitamente autorizzato.

1. In **Inspector > Dashboard**, registrare ciò che è visibile anche quando i valori sono zero.
2. In **Findings**, verificare se l'elenco è vuoto o non accessibile.
3. In **Account management/Coverage**, cercare risorse EC2, immagini ECR o funzioni Lambda coperte.
4. Classificare lo stato osservato scegliendo una sola frase:
   - servizio attivo, risorse coperte, nessun finding;
   - servizio attivo, ma nessuna risorsa coperta;
   - servizio non attivo;
   - stato non verificabile per autorizzazioni insufficienti.
5. Scrivere quale evidenza aggiuntiva servirebbe prima di affermare che l'ambiente non contiene vulnerabilità note.

## Parte D - Triage Inspector su casi forniti

I casi seguenti sono sintetici, ma contengono dati sufficienti per svolgere un triage completo.

| Caso | Risorsa e contesto                                       | Finding                                                                        | Correzione              | Copertura                                     |
| ---- | -------------------------------------------------------- | ------------------------------------------------------------------------------ | ----------------------- | --------------------------------------------- |
| V1   | EC2 esposta a Internet, applicazione con dati clienti    | Libreria con gravità `CRITICAL`, exploit noto e pacchetto corretto disponibile | Patch disponibile       | Attiva, scansione recente                     |
| V2   | Immagine ECR di test, non distribuita, dati sintetici    | Pacchetto con gravità `HIGH`, nessun exploit osservato                         | Patch disponibile       | Attiva, scansione recente                     |
| V3   | Funzione Lambda pubblica che elabora documenti riservati | Dipendenza con gravità `HIGH` e codice di exploit pubblico                     | Nessuna patch immediata | Attiva, scansione recente                     |
| V4   | Istanza EC2 di produzione                                | Nessun finding visualizzato                                                    | Non determinabile       | Agente non connesso; ultima scansione assente |

1. Ordinare V1, V3 e V2 per priorità e motivare ogni posizione.
2. Trattare V4 come lacuna di copertura, non come risorsa priva di vulnerabilità.
3. Per ogni caso indicare:
   - responsabile;
   - intervento o mitigazione;
   - tempo obiettivo;
   - prova di chiusura;
   - rischio residuo.
4. Per V3 proporre almeno una mitigazione temporanea verificabile in attesa della patch.
5. Per V4 indicare come ripristinare la copertura e quale controllo ripetere dopo il ripristino.

## Output atteso

Creare `consegna10.md` con:

1. stato iniziale di GuardDuty e Inspector;
2. schede G1 e G2, oppure motivazione documentata dell'impossibilità di generarle;
3. classificazione dello stato di copertura Inspector;
4. priorità V1, V3, V2 e trattamento di V4;
5. responsabile, intervento, tempo, prova di chiusura e rischio residuo per ogni caso;
6. distinzione esplicita tra dato reale osservato e caso SAMPLE/sintetico.

## Checkpoint

- `[SAMPLE]` non è un incidente reale.
- Una vulnerabilità non prova uno sfruttamento.
- Zero finding non significa automaticamente zero vulnerabilità.
- Una risorsa non coperta rappresenta una lacuna nelle evidenze.
- La presenza di `inspector:*` in una policy non autorizza automaticamente le API `inspector2:*`.

## Troubleshooting rapido

- **Summary tutto a zero:** verificare regione, stato del detector e data di aggiornamento; poi generare sample se consentito.
- **Sample non visibili:** aprire Findings, rimuovere filtri incompatibili e cercare `[SAMPLE]`.
- **Detector condiviso:** sola lettura; non eliminarlo e non modificarne le impostazioni.
- **Inspector vuoto:** controllare prima Coverage; distinguere nessun finding da nessuna risorsa analizzata.
- **Access denied:** registrare servizio, pagina e azione negata; non modificare IAM.

## Cleanup obbligatorio

- Archiviare i sample se il docente vuole mantenere il detector e l'account lo consente.
- Eliminare il detector solo se è stato creato personalmente durante questo lab:

```bash
aws guardduty delete-detector --detector-id DETECTOR_ID --region us-east-1
```

- Verificare il risultato con `list-detectors`.
- Non eliminare detector o configurazioni condivise.
- Eliminare eventuali estratti contenenti identificativi non anonimizzati e segnalare costi o risorse residue.

## Riferimenti AWS

- [Generazione di finding SAMPLE in GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/sample_findings.html)
- [Dashboard di Amazon Inspector](https://docs.aws.amazon.com/inspector/latest/user/understanding-dashboard.html)
- [Valutare la copertura di Amazon Inspector](https://docs.aws.amazon.com/inspector/latest/user/assessing-coverage.html)
