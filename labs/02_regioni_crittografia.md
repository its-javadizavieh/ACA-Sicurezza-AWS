# Lab 02 - Regioni e crittografia dei dati

## Obiettivo
Raccogliere evidenze separate sulla collocazione e sulla crittografia di un oggetto sintetico.

## Prerequisiti
Sessione Academy, console o AWS CLI con credenziali temporanee, regione `us-east-1`. Usare un suffisso personale univoco nei nomi.

## Scenario
Un rapporto di manutenzione non deve diventare pubblico. Si vuole documentare dove viene conservato e quale protezione ha, senza distribuire server.

## Step (numerati)

1. Aprire EC2 -> Network and Security -> Availability Zones.
2. Annotare almeno due coppie `Zone name` e `Zone ID`.
3. Aprire S3 -> Buckets -> Create bucket.
4. Nome bucket: `sicurezza02-SUFFISSO`.
5. Regione: `US East (N. Virginia) us-east-1`.
6. Object Ownership: ACL disabilitate.
7. Block Public Access: lasciare tutte le opzioni attive.
8. Bucket Versioning: disabilitato.
9. Default encryption: SSE-S3.
10. Creare localmente `rapporto.txt` con testo sintetico.
11. Nel bucket -> Upload -> caricare `rapporto.txt`.
12. Aprire l'oggetto -> Properties -> verificare `Server-side encryption: Amazon S3 managed keys (SSE-S3)`.
13. Scaricare l'oggetto dalla console e confrontarlo con il file originale.
14. Scrivere in consegna tre evidenze separate: regione/zone, cifratura a riposo, download autenticato.

## Svolgimento guidato

Configurazione corretta:

- regione: `US East (N. Virginia) us-east-1`;
- bucket: `sicurezza02-SUFFISSO`;
- Object Ownership: ACL disabilitate;
- Block Public Access: tutte le opzioni attive;
- versioning: disabilitato per questo lab;
- cifratura predefinita: SSE-S3;
- oggetto caricato: `rapporto.txt` con contenuto sintetico.

Evidenze da riportare:

- almeno due coppie `Zone name` e `Zone ID` viste in EC2;
- proprieta oggetto con `Server-side encryption: Amazon S3 managed keys (SSE-S3)`;
- download autenticato riuscito e contenuto uguale all'originale;
- bucket non pubblico.

Verifica opzionale da CLI, compatibile con Ubuntu, macOS e Windows PowerShell:

    aws ec2 describe-availability-zones --region us-east-1 --query 'AvailabilityZones[].[ZoneName,ZoneId,State]' --output table
    aws s3api head-object --bucket sicurezza02-SUFFISSO --key rapporto.txt --region us-east-1 --query '{Encryption:ServerSideEncryption,Size:ContentLength}'

Controllo finale:

- `ServerSideEncryption` deve risultare `AES256`;
- il contenuto scaricato deve coincidere con l'originale;
- il bucket deve restare privato.

## Output atteso
`consegna02.md`: zone, configurazione S3, prova di lettura e tre conclusioni separate su posizione, cifratura e accesso.

## Checkpoint
Il contenuto scaricato coincide con l'originale. La cifratura non viene interpretata come limite ai lettori già autorizzati.

## Troubleshooting rapido
Nome bucket occupato: cambia suffisso. CLI assente: usa console. `AccessDenied`: controlla sessione e regione, poi usa il caso sintetico senza cambiare ruolo.

## Cleanup obbligatorio
Elimina `rapporto.txt` dal bucket, verifica che sia vuoto ed elimina esclusivamente il bucket creato. Se hai attivato versionamento per errore, rimuovi anche versioni e marcatori prima del bucket. Elimina download e URL firmati dalle note. Conferma l'assenza del bucket; nel percorso locale dichiara che non sono state create risorse.
