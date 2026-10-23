# Lab 07 - Connessioni sicure e CloudTrail

## Obiettivo
Motivare una connessione cifrata e attribuire un'operazione AWS a una sessione.

## Prerequisiti
Sessione Academy, `us-east-1`, console CloudWatch Logs e CloudTrail o CLI. Nessun trail, VPN o Direct Connect da creare.

## Scenario
Una sede vuole inviare rapporti riservati ad AWS. Un operatore crea una risorsa di registrazione e occorre ricostruire chi ha eseguito l'azione.

## Step (numerati)

1. In `consegna07.md` descrivere due alternative:
   - Site-to-Site VPN con tunnel IPsec;
   - Direct Connect con cifratura progettata separatamente.
2. Aprire CloudWatch -> Logs -> Log groups.
3. Selezionare Create log group.
4. Nome: `/sicurezza07/SUFFISSO`.
5. Creare il gruppo log vuoto.
6. Annotare l'ora UTC della creazione.
7. Aprire CloudTrail -> Event history.
8. Regione: `us-east-1`.
9. Filtro: Event name = `CreateLogGroup`.
10. Intervallo: includere l'ora UTC annotata.
11. Aprire l'evento del proprio gruppo log.
12. Annotare `eventTime`, `eventName`, `eventSource`, `userIdentity.type`, `requestParameters.logGroupName`, eventuale `errorCode`.

## Svolgimento guidato

Confronto da scrivere in `consegna07.md`:

- Site-to-Site VPN: usa tunnel IPsec e protegge la comunicazione su Internet;
- Direct Connect: offre connettivita dedicata, ma la cifratura va progettata separatamente.

Evento CloudTrail da cercare:

- servizio: CloudWatch Logs;
- evento: `CreateLogGroup`;
- log group: `/sicurezza07/SUFFISSO`;
- campi da annotare: `eventTime`, `eventName`, `eventSource`, `userIdentity.type`, `requestParameters.logGroupName`, eventuale `errorCode`.

Comandi opzionali se la console e' lenta, compatibili con Ubuntu, macOS e Windows PowerShell:

    aws logs create-log-group --log-group-name /sicurezza07/SUFFISSO --region us-east-1
    aws cloudtrail lookup-events --lookup-attributes AttributeKey=EventName,AttributeValue=CreateLogGroup --region us-east-1

Controllo finale:

- l'evento deve mostrare `CreateLogGroup` da `logs.amazonaws.com`;
- assenza di `errorCode` significa operazione riuscita;
- l'evento dimostra una chiamata di gestione AWS, non traffico applicativo e non uso di VPN.

Fallback:

- se l'evento non appare entro 15 minuti, usare il caso sintetico previsto dal lab e dichiarare la simulazione.

Cleanup da CLI opzionale, compatibile con Ubuntu, macOS e Windows PowerShell:

    aws logs delete-log-group --log-group-name /sicurezza07/SUFFISSO --region us-east-1

## Output atteso
`consegna07.md`: due schemi di collegamento, evento analizzato, tre fatti e due limiti delle evidenze.

## Checkpoint
`CreateLogGroup` è un evento di gestione. L'identità assunta è distinta dalla persona da attribuire tramite ulteriori dati di sessione.

## Troubleshooting rapido
Nessun evento: controlla regione, filtro e intervallo UTC. Credenziali scadute: rinnova dal portale. Mancanza CLI: usa console; se entrambe non disponibili, caso sintetico.

## Cleanup obbligatorio
Elimina solo il gruppo creato: `aws logs delete-log-group --log-group-name /sicurezza07/SUFFISSO --region us-east-1`. Verifica l'assenza in console. L'evento di gestione storico resta nell'account. Elimina esportazioni non anonimizzate.
