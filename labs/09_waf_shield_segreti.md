# Lab 09 - WAF, Shield e gestione dei segreti

## Obiettivo
Osservare una configurazione WAF e gestire due versioni di un segreto sintetico senza distribuire applicazioni.

## Prerequisiti
Console Academy, `us-east-1`, autorizzazioni `wafv2:*` e `secretsmanager:*` presenti. WAF e segreti possono generare costi: creare solo le risorse indicate e cancellarle nella stessa sessione. Non usare credenziali reali.

## Scenario
Un percorso amministrativo va osservato prima di introdurre un blocco. Un'applicazione deve recuperare una versione corrente della propria credenziale.

## Step (numerati)

1. WAF: Aprire WAF and Shield -> Web ACLs -> Create web ACL.
2. Resource type: Regional resources.
3. Region: `US East (N. Virginia)`.
4. Nome: `sicurezza09-SUFFISSO`.
5. Associated AWS resources: nessuna associazione.
6. Default action: Allow.
7. Add rules -> Add my own rules and rule groups -> Rule builder.
8. Nome regola: `osserva-admin`.
9. Statement: URI path starts with `/admin`.
10. Text transformation: `NONE`.
11. Action: Count.
12. Priority: 0.
13. Salvare la Web ACL.
14. Secrets Manager: Aprire Secrets Manager -> Store a new secret.
15. Secret type: Other type of secret.
16. Key/value: `password` = `DEMO-NON-VALIDO-v1`.
17. Encryption key: `aws/secretsmanager`.
18. Secret name: `sicurezza09/SUFFISSO`.
19. Rotation: disabilitata.
20. Replica: disabilitata.
21. Creare il segreto.
22. Aprire il segreto -> Retrieve secret value.
23. Aggiornare il valore a `DEMO-NON-VALIDO-v2`.
24. Aprire Versions e verificare `AWSCURRENT` e `AWSPREVIOUS`.

## Svolgimento guidato

Configurazione WAF corretta:

- Web ACL: `sicurezza09-SUFFISSO`;
- tipo risorsa: Regional resources;
- regione: `US East (N. Virginia)`;
- risorse associate: nessuna;
- default action: Allow;
- regola: `osserva-admin`;
- statement: URI path starts with `/admin`;
- text transformation: `NONE`;
- action: Count;
- priority: 0.

Match WAF attesi:

- `/`: non corrisponde;
- `/admin`: corrisponde;
- `/admin/report`: corrisponde;
- `/administrator`: corrisponde;
- `/Admin`: non corrisponde con confronto case-sensitive.

Configurazione Secrets Manager corretta:

- tipo segreto: Other type of secret;
- key/value iniziale: `password` = `DEMO-NON-VALIDO-v1`;
- encryption key: `aws/secretsmanager`;
- nome segreto: `sicurezza09/SUFFISSO`;
- rotation: disabilitata;
- replica: disabilitata;
- valore aggiornato: `DEMO-NON-VALIDO-v2`.

Controllo opzionale delle versioni, compatibile con Ubuntu, macOS e Windows PowerShell:

    aws secretsmanager list-secret-version-ids --secret-id sicurezza09/SUFFISSO --region us-east-1

Controllo finale:

- il valore aggiornato deve essere `AWSCURRENT`;
- il valore precedente deve essere `AWSPREVIOUS`;
- non usare segreti reali;
- non attivare Shield Advanced o test di carico.

Cleanup da CLI opzionale, compatibile con Ubuntu, macOS e Windows PowerShell:

    aws secretsmanager delete-secret --secret-id sicurezza09/SUFFISSO --recovery-window-in-days 7 --region us-east-1

## Output atteso
`consegna09.md`: cinque esiti Count/Block, prova dell'assenza di associazioni, etichette delle versioni e scelta motivata dei controlli.

## Checkpoint
`/administrator` corrisponde al prefisso e rivela un possibile falso positivo. Il segreto corrente è v2. Nessuna protezione del traffico reale è stata collaudata.

## Troubleshooting rapido
WAF non visibile: verifica regione e ambito regionale. Nome segreto già usato o in cancellazione: scegli un nuovo suffisso. Accesso negato: passa al caso locale senza modificare ruoli.

## Cleanup obbligatorio
Elimina la sola Web ACL del lab e verifica l'assenza. Nel segreto scegli eliminazione pianificata con 7 giorni di recupero; verifica la data di cancellazione, senza dichiararlo già rimosso. Non usare eliminazione forzata. Non essendoci replica, rotazione o risorse associate non devono restare ulteriori dipendenze. Registra eventuali operazioni fallite per il docente.
