# Lab 04 - IAM: buone pratiche, MFA e policy JSON

## Obiettivo
Valutare una policy senza creare utenti, dispositivi MFA o allegati IAM.

## Prerequisiti
Editor; CLI facoltativa. `iam:Sim*` è presente nella policy fornita. Non occorre che il bucket fittizio esista; il simulatore non esegue operazioni S3.

## Scenario
Un lettore può scaricare rapporti dal prefisso `report/`, ma non può cancellarli o leggere altri prefissi.

## Step (numerati)

1. Creare `policy04.json` localmente.
2. Inserire una dichiarazione `Allow` per `s3:GetObject` su `arn:aws:s3:::didattica-esempio/report/*`.
3. Inserire una dichiarazione `Deny` per `s3:DeleteObject` su `arn:aws:s3:::didattica-esempio/*`.
4. Non allegare la policy a utenti, gruppi o ruoli.
5. Aprire IAM -> Policy simulator, se accessibile.
6. Usare una policy custom e testare `s3:GetObject` e `s3:DeleteObject` sull'oggetto `report/a.txt`.
7. Testare `s3:GetObject` su `privato/a.txt`.
8. Testare `s3:ListBucket` sull'ARN del bucket.
9. In `consegna04.md` indicare decisione e motivo per ogni caso.
10. Analizzare separatamente la condizione MFA con `BoolIfExists`.

## Svolgimento guidato

Contenuto logico di `policy04.json`:

- `Allow` per `s3:GetObject` su `arn:aws:s3:::didattica-esempio/report/*`;
- `Deny` per `s3:DeleteObject` su `arn:aws:s3:::didattica-esempio/*`;
- nessun `Allow` per `s3:ListBucket`;
- nessun collegamento della policy a utenti, gruppi o ruoli reali.

Decisioni attese:

- lettura `report/a.txt`: consentita;
- cancellazione `report/a.txt`: negata esplicitamente;
- lettura `privato/a.txt`: negata implicitamente;
- elenco bucket: negato implicitamente;
- MFA `true`: il Deny condizionale non si applica;
- MFA `false`: il Deny condizionale si applica;
- MFA assente: il Deny si applica con `BoolIfExists`.

Simulazione opzionale da CLI, compatibile con Ubuntu, macOS e Windows PowerShell:

    aws iam simulate-custom-policy --policy-input-list file://policy04.json --action-names s3:GetObject s3:DeleteObject --resource-arns arn:aws:s3:::didattica-esempio/report/a.txt --region us-east-1 --query 'EvaluationResults[].[EvalActionName,EvalDecision]'

    aws iam simulate-custom-policy --policy-input-list file://policy04.json --action-names s3:GetObject --resource-arns arn:aws:s3:::didattica-esempio/privato/a.txt --region us-east-1 --query 'EvaluationResults[].[EvalActionName,EvalDecision]'
    
    aws iam simulate-custom-policy --policy-input-list file://policy04.json --action-names s3:ListBucket --resource-arns arn:aws:s3:::didattica-esempio --region us-east-1 --query 'EvaluationResults[].[EvalActionName,EvalDecision]'

## Output atteso
`consegna04.md`: quattro decisioni, confronto manuale/simulato e tre casi MFA. Specificare se la simulazione AWS è stata effettivamente eseguita.

## Checkpoint
Risultati: lettura report consentita, cancellazione negata esplicitamente, lettura privata ed elenco negati implicitamente. La condizione Deny MFA si applica per `false` e per chiave assente, non per `true`.

## Troubleshooting rapido
Errore nel file: controlla virgolette e virgole. Azione S3 e ARN incompatibili: separa bucket e oggetto. Simulatore negato: usa gli stessi casi manualmente e registra il limite.

## Cleanup obbligatorio
Non è stata creata alcuna risorsa. Conserva la policy come esercizio oppure elimina la copia temporanea; non allegarla a utenti o ruoli e non modificare MFA del portale.
