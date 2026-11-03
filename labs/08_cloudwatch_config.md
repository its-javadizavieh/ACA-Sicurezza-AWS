# Lab 08 - CloudWatch e AWS Config

## Obiettivo
Configurare un allarme osservabile e applicare una regola di conformità a evidenze complete e incomplete.

## Prerequisiti
Sessione Academy, `us-east-1`, CLI o console. Metriche personalizzate e allarmi possono consumare budget anche senza server.

## Scenario
Una metrica sintetica conta errori di accesso. Separatamente, una regola richiede che i volumi EBS nel perimetro siano cifrati.

## Step (numerati)

1. CloudWatch: Aprire CloudWatch -> All metrics.
2. Pubblicare una metrica custom con CLI, perche dalla console non si inserisce comodamente un nuovo datapoint.
3. Aprire CloudWatch -> Alarms -> All alarms -> Create alarm.
4. Selezionare metrica `Sicurezza08`, `ErroriAccesso`, dimensione `Studente=SUFFISSO`.
5. Statistica: Sum.
6. Periodo: 1 minuto.
7. Soglia: maggiore di 3.
8. Nome allarme: `sicurezza08-SUFFISSO`.
9. Actions: disabilitate o nessuna notifica.
10. Creare l'allarme.
11. Aprire il dettaglio dell'allarme e osservare stato e cronologia.
12. Pubblicare valore 0 in un periodo successivo e osservare eventuale ritorno a `OK`.
13. AWS Config: Aprire AWS Config -> Settings.
14. Verificare se AWS Config e' gia attivo nella regione.
15. Se AWS Config non e' attivo, completare il progetto della regola come caso guidato locale.
16. Aprire AWS Config -> Rules solo in lettura.
17. In consegna progettare la regola `ENCRYPTED_VOLUMES`.
18. Indicare prerequisiti: recorder, service-linked role, delivery channel S3, scope `AWS::EC2::Volume`.
19. Valutare i quattro casi sintetici A, B, C, D.

## Svolgimento guidato

Configurazione CloudWatch corretta:

- namespace metrica: `Sicurezza08`;
- nome metrica: `ErroriAccesso`;
- dimensione: `Studente=SUFFISSO`;
- primo valore pubblicato: `5`;
- allarme: `sicurezza08-SUFFISSO`;
- statistica: Sum;
- periodo: 1 minuto;
- soglia: maggiore di 3;
- azioni: disabilitate o nessuna notifica;
- valore successivo: `0` per osservare il ritorno a `OK` dopo valutazione.

Comandi per la metrica e l'allarme, compatibili con Ubuntu, macOS e Windows PowerShell:

    aws cloudwatch put-metric-data --namespace Sicurezza08 --metric-name ErroriAccesso --dimensions Studente=SUFFISSO --value 5 --unit Count --region us-east-1

    aws cloudwatch put-metric-alarm --alarm-name sicurezza08-SUFFISSO --namespace Sicurezza08 --metric-name ErroriAccesso --dimensions Name=Studente,Value=SUFFISSO --statistic Sum --period 60 --evaluation-periods 1 --threshold 3 --comparison-operator GreaterThanThreshold --treat-missing-data missing --no-actions-enabled --region us-east-1
    
    aws cloudwatch put-metric-data --namespace Sicurezza08 --metric-name ErroriAccesso --dimensions Studente=SUFFISSO --value 0 --unit Count --region us-east-1

Risposte AWS Config:

- se AWS Config non e' attivo, usare il caso guidato locale;
- regola progettata: `ENCRYPTED_VOLUMES`;
- prerequisiti: recorder, service-linked role, delivery channel S3, scope `AWS::EC2::Volume`;
- A cifrato e collegato: conforme;
- B non cifrato e collegato: non conforme;
- C non collegato: non applicabile;
- D non registrato: non valutabile.

Controllo finale:

- valore 5: stato atteso `ALARM`;
- valore 0: stato atteso `OK` dopo valutazione.

## Output atteso
`consegna08.md`: parametri e stati dell'allarme, progetto Config, quattro valutazioni e limiti del percorso realmente eseguito.

## Checkpoint
A conforme, B non conforme, C non applicabile, D non valutabile con l'evidenza disponibile. Un allarme in stato OK non prova la conformità dei volumi.

## Troubleshooting rapido
Metrica assente: controlla regione, namespace e dimensione. Allarme senza dati: verifica timestamp e periodo. Config non attivo o non accessibile: completa il progetto e il caso locale.

## Cleanup obbligatorio
`aws cloudwatch delete-alarms --alarm-names sicurezza08-SUFFISSO --region us-east-1`. Verifica l'assenza. Interrompi ogni invio: le metriche già pubblicate non si cancellano manualmente e restano secondo la retention AWS. Non sono stati creati recorder o canali AWS Config.
