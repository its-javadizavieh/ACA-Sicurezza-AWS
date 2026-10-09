# Lab 05 - SCP e analisi degli accessi

## Obiettivo
Valutare limiti organizzativi e accessi esterni mediante casi interamente locali.

## Prerequisiti
Lezione 04, editor Markdown. Organizations e Access Analyzer non risultano autorizzati; non tentare attivazione o creazione di risorse.

## Scenario
Gerarchia sintetica: radice -> OU Produzione -> account Fabbrica. A tutti i livelli è presente `FullAWSAccess`. Sulla OU è aggiunta questa SCP, da leggere senza distribuirla:

```json
{"Version":"2012-10-17","Statement":[{"Effect":"Deny","Action":"s3:DeleteObject","Resource":"*"}]}
```

Il ruolo applicativo dell'account membro ha una policy IAM che consente `s3:GetObject` e `s3:DeleteObject` su `arn:aws:s3:::rapporti-demo/*`. Non ci sono altre policy nel modello.

## Step (numerati)

1. Creare `consegna05.md`.
2. Disegnare la gerarchia: root -> OU Produzione -> account Fabbrica.
3. Annotare `FullAWSAccess` a tutti i livelli.
4. Aggiungere nel modello la SCP con `Deny` su `s3:DeleteObject`.
5. Aggiungere nel modello la policy IAM del ruolo applicativo con `s3:GetObject` e `s3:DeleteObject`.
6. Valutare `s3:GetObject`, `s3:DeleteObject`, `s3:PutObject`.
7. Ripetere la valutazione togliendo solo la SCP Deny.
8. Analizzare Finding A: accesso partner troppo ampio.
9. Analizzare Finding B: lettura anonima non approvata.
10. Scrivere cosa manca per provare accesso realmente avvenuto.

## Svolgimento guidato

Valutazione con SCP attiva:

- `s3:GetObject`: consentito, perche IAM permette l'azione e la SCP non la nega;
- `s3:DeleteObject`: negato esplicitamente dalla SCP;
- `s3:PutObject`: negato implicitamente per assenza di `Allow` IAM.

Valutazione senza SCP Deny:

- `s3:GetObject`: consentito;
- `s3:DeleteObject`: consentito dalla policy IAM;
- `s3:PutObject`: negato implicitamente.

Finding A:

- problema: accesso partner troppo ampio;
- correzione: limitare il partner a `export/*`;
- prova positiva: partner legge in `export/`;
- prova negativa: partner non legge fuori `export/`.

Finding B:

- problema: lettura anonima non approvata;
- correzione: rimuovere accesso anonimo e mantenere blocco accesso pubblico;
- prova negativa: richiesta anonima non legge.

Nota operativa:

- nessun comando AWS richiesto, perche Organizations e Access Analyzer sono trattati come esercizio locale in questo lab.

## Output atteso
`consegna05.md`: matrice di decisione prima/dopo e due revisioni con proprietario, correzione, prova e limite delle evidenze.

## Checkpoint
Prima: Get consentito, Delete negato esplicitamente, Put negato implicitamente. Dopo: cambia soltanto Delete. La condivisione partner richiede riduzione del prefisso, non accettazione indiscriminata.

## Troubleshooting rapido
Se interpreti `FullAWSAccess` come autorizzazione IAM aggiunta, separa il limite organizzativo dalla concessione. Tutti i dati necessari sono nello scenario: non occorre accesso al servizio.

## Cleanup obbligatorio
Nessuna risorsa cloud. Conserva i casi come simulazioni, senza sostituire gli identificativi fittizi con quelli di partner reali.
