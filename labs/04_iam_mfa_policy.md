# Lab 04 - IAM: buone pratiche, MFA e policy JSON

## Obiettivo e scenario

Valutare una policy senza creare utenti, dispositivi MFA o allegati IAM. Un lettore può scaricare rapporti dal prefisso `report/`, ma non può cancellarli o leggere altri prefissi. Nella seconda parte, la lettura dei rapporti richiede MFA.

## Prerequisiti

Accesso a IAM Policy Simulator con permessi di simulazione IAM (`iam:Sim*` nella policy del laboratorio). Le policy JSON riportate sotto sono gli oggetti del test, non concedono i permessi per usare il simulatore. Non occorre che il bucket fittizio esista: il simulatore non esegue operazioni S3.

I JSON sono inclusi in questa guida e si possono copiare direttamente nella console. La CLI è facoltativa.

## 1. Caricare la policy base

Aprire [IAM Policy Simulator](https://policysim.aws.amazon.com/), scegliere la modalità **Custom** e inserire questa policy nel relativo editor. Non crearla o allegarla a utenti, gruppi o ruoli IAM reali.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowReadReports",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::didattica-esempio/report/*"
    },
    {
      "Sid": "DenyDeleteObjects",
      "Effect": "Deny",
      "Action": "s3:DeleteObject",
      "Resource": "arn:aws:s3:::didattica-esempio/*"
    }
  ]
}
```

## 2. Configurare le risorse e simulare i quattro casi base

Selezionare Amazon S3 e l'azione `GetObject`. Nella finestra **Edit resources** per `S3:GetObject`, compilare:

| Campo | Valore |
|---|---|
| Selezione | **Specific** |
| Type | `object` |
| ARN | `arn:aws:s3:::didattica-esempio/report/a.txt` |
| Apply to all actions with the same resource type | Lasciare la casella non selezionata |

Cliccare **Save**, quindi eseguire la simulazione. Ripetere per ogni riga della tabella, cambiando azione e risorsa. Per `ListBucket`, usare il tipo `bucket` e l'ARN del solo bucket.

| Azione | ARN della risorsa | Decisione attesa | Motivo |
|---|---|---|---|
| `s3:GetObject` | `arn:aws:s3:::didattica-esempio/report/a.txt` | `allowed` | Allow sul prefisso `report/` |
| `s3:DeleteObject` | `arn:aws:s3:::didattica-esempio/report/a.txt` | `explicitDeny` | Deny sulla cancellazione |
| `s3:GetObject` | `arn:aws:s3:::didattica-esempio/privato/a.txt` | `implicitDeny` | Nessun Allow sul prefisso `privato/` |
| `s3:ListBucket` | `arn:aws:s3:::didattica-esempio` | `implicitDeny` | Nessun Allow per elencare il bucket |

## 3. Sostituire la policy con la variante MFA

Nell'editor della policy custom, sostituire il JSON precedente con questo JSON completo:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowReadReports",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::didattica-esempio/report/*"
    },
    {
      "Sid": "DenyDeleteObjects",
      "Effect": "Deny",
      "Action": "s3:DeleteObject",
      "Resource": "arn:aws:s3:::didattica-esempio/*"
    },
    {
      "Sid": "DenyReadReportsWithoutMFA",
      "Effect": "Deny",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::didattica-esempio/report/*",
      "Condition": {
        "BoolIfExists": {
          "aws:MultiFactorAuthPresent": "false"
        }
      }
    }
  ]
}
```

La dichiarazione `DenyReadReportsWithoutMFA` aggiunge un Deny condizionale alla lettura dei rapporti. Con `BoolIfExists` e valore `false`, il Deny si applica sia quando MFA è `false`, sia quando la chiave è assente. Con MFA `true`, il Deny condizionale non si applica e l'Allow consente la lettura.

## 4. Simulare i tre casi MFA

Selezionare `s3:GetObject` e impostare nuovamente la risorsa `arn:aws:s3:::didattica-esempio/report/a.txt` come al punto 2.

Impostare la chiave booleana `aws:MultiFactorAuthPresent` **nel contesto della simulazione, non nella finestra Edit resources**. Eseguire una simulazione per ogni caso:

| Valore MFA nel contesto | Decisione attesa |
|---|---|
| `true` | `allowed` |
| `false` | `explicitDeny` |
| Chiave omessa | `explicitDeny` |

Per il terzo caso, omettere la chiave dal contesto, senza inserire la parola `assente`. Se l'interfaccia richiede un valore, usare il comando CLI senza `--context-entries` riportato sotto.

Il valore MFA viene simulato: l'esercizio non abilita MFA e non verifica un dispositivo reale. Il Deny MFA riguarda solo la lettura del prefisso `report/`; gli altri casi mantengono le decisioni della policy base.

## CLI facoltativa

Solo se si usa la CLI, copiare il JSON del punto 1 in un file locale `policy04.json` e quello del punto 3 in `policy04_mfa.json`. Eseguire i comandi dalla cartella scelta, con credenziali autorizzate alla simulazione IAM.

Casi base:

```sh
aws iam simulate-custom-policy --policy-input-list file://policy04.json --action-names s3:GetObject s3:DeleteObject --resource-arns arn:aws:s3:::didattica-esempio/report/a.txt --query 'EvaluationResults[].[EvalActionName,EvalDecision]'

aws iam simulate-custom-policy --policy-input-list file://policy04.json --action-names s3:GetObject --resource-arns arn:aws:s3:::didattica-esempio/privato/a.txt --query 'EvaluationResults[].[EvalActionName,EvalDecision]'

aws iam simulate-custom-policy --policy-input-list file://policy04.json --action-names s3:ListBucket --resource-arns arn:aws:s3:::didattica-esempio --query 'EvaluationResults[].[EvalActionName,EvalDecision]'
```

Casi MFA, nell'ordine `true`, `false`, chiave omessa:

```sh
aws iam simulate-custom-policy --policy-input-list file://policy04_mfa.json --action-names s3:GetObject --resource-arns arn:aws:s3:::didattica-esempio/report/a.txt --context-entries ContextKeyName=aws:MultiFactorAuthPresent,ContextKeyValues=true,ContextKeyType=boolean --query 'EvaluationResults[].[EvalActionName,EvalDecision]'

aws iam simulate-custom-policy --policy-input-list file://policy04_mfa.json --action-names s3:GetObject --resource-arns arn:aws:s3:::didattica-esempio/report/a.txt --context-entries ContextKeyName=aws:MultiFactorAuthPresent,ContextKeyValues=false,ContextKeyType=boolean --query 'EvaluationResults[].[EvalActionName,EvalDecision]'

aws iam simulate-custom-policy --policy-input-list file://policy04_mfa.json --action-names s3:GetObject --resource-arns arn:aws:s3:::didattica-esempio/report/a.txt --query 'EvaluationResults[].[EvalActionName,EvalDecision]'
```

## Consegna

In `consegna04.md`, registrare decisione e motivo per i quattro casi base e i tre casi MFA. Confrontare le previsioni con i risultati ottenuti e specificare se la simulazione AWS è stata effettivamente eseguita.

## Troubleshooting rapido

- **Errore nel JSON:** copiare solo il contenuto del blocco, senza i delimitatori Markdown; controllare virgolette e virgole.
- **Azione S3 e ARN incompatibili:** usare l'ARN dell'oggetto per `GetObject` e `DeleteObject`, quello del bucket per `ListBucket`.
- **Simulatore negato:** valutare manualmente gli stessi casi e indicare il limite nella consegna.

## Cleanup

La simulazione non crea risorse AWS. Se sono state salvate copie locali per la CLI, conservarle come esercizio oppure eliminarle. Non allegare le policy a utenti o ruoli e non modificare MFA del portale.

Riferimento: [AWS — aws:MultiFactorAuthPresent e BoolIfExists](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-multifactorauthpresent).
