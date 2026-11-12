# Lab 11 - Backup e ripristino sicuro

## Obiettivo
Dimostrare il recupero di una risorsa dedicata e distinguere tale prova dalla validazione del contenuto applicativo.

## Prerequisiti
Console EC2 e AWS Backup in `us-east-1`, sessione Academy. Selezionare il ruolo esistente predisposto per il laboratorio nei campi IAM role e Restore role; non creare un nuovo ruolo. Disponibilità effettiva e ulteriori vincoli vanno verificati. Nessuna istanza necessaria.

## Scenario
Si vuole recuperare un piccolo volume in una risorsa nuova senza toccare il sistema originale. Per limitare tempi e costi il volume non contiene un'applicazione: la prova del contenuto usa un file locale distinto.

## Step (numerati)

1. EBS: Aprire EC2 -> Elastic Block Store -> Volumes.
2. Selezionare Create volume.
3. Tipo: gp3.
4. Size: 1 GiB.
5. Availability Zone: una AZ di `us-east-1`.
6. Encryption: enabled, chiave `aws/ebs`.
7. Tag Name: `sicurezza11-SUFFISSO`.
8. Creare il volume e attendere stato `available`.
9. Non collegare il volume a istanze.
10. AWS Backup: Aprire AWS Backup -> Backup vaults.
11. Creare vault `sicurezza11-SUFFISSO`.
12. Non attivare Vault Lock.
13. Aprire Protected resources o Create on-demand backup.
14. Resource type: EBS.
15. Resource ID: volume creato nel lab.
16. Backup vault: vault dedicato.
17. IAM role: ruolo esistente predisposto per il laboratorio; non creare automaticamente AWSBackupDefaultServiceRole.
18. Retention: 1 giorno.
19. Cold storage: non usato.
20. Avviare backup e attendere `COMPLETED`.
21. Restore: Aprire AWS Backup -> Backup vaults -> vault del lab.
22. Aprire il recovery point creato.
23. Selezionare Restore.
24. Restore type: nuovo volume EBS.
25. Availability Zone: stessa zona del volume origine.
26. Tipo: gp3.
27. Dimensione: 1 GiB o minima richiesta.
28. Encryption: coerente con origine.
29. IAM role: ruolo esistente predisposto per il laboratorio; non creare automaticamente AWSBackupDefaultServiceRole.
30. Avviare restore e attendere `COMPLETED`.
31. Aprire EC2 -> Volumes e annotare il nuovo volume ID.

## Svolgimento guidato

Configurazione EBS corretta:

- volume origine: gp3, 1 GiB, una AZ di `us-east-1`;
- cifratura: enabled con chiave `aws/ebs`;
- tag Name: `sicurezza11-SUFFISSO`;
- stato atteso prima del backup: `available`;
- volume non collegato a istanze.

Configurazione AWS Backup corretta:

- vault: `sicurezza11-SUFFISSO`;
- Vault Lock: non attivato;
- resource type: EBS;
- resource ID: volume creato nel lab;
- retention: 1 giorno;
- cold storage: non usato;
- stato backup atteso: `COMPLETED`.

Restore corretto:

- recovery point: quello creato nel vault del lab;
- restore type: nuovo volume EBS;
- Availability Zone: stessa zona del volume origine;
- tipo: gp3;
- dimensione: 1 GiB o minima richiesta;
- cifratura: coerente con origine;
- stato restore atteso: `COMPLETED`.

Prova locale solo per spiegare il confronto di integrita.

Ubuntu:

    printf '%s' 'rapporto sintetico 11' > originale11.txt

    cp originale11.txt copia11.txt

    cp copia11.txt recuperato11.txt

    sha256sum originale11.txt recuperato11.txt
    
    cmp originale11.txt recuperato11.txt

macOS:

    printf '%s' 'rapporto sintetico 11' > originale11.txt

    cp originale11.txt copia11.txt

    cp copia11.txt recuperato11.txt

    shasum -a 256 originale11.txt recuperato11.txt

    cmp originale11.txt recuperato11.txt

Windows PowerShell:

    Set-Content -Path originale11.txt -Value "rapporto sintetico 11"

    Copy-Item originale11.txt copia11.txt

    Copy-Item copia11.txt recuperato11.txt

    Get-FileHash originale11.txt,recuperato11.txt -Algorithm SHA256

    Compare-Object (Get-Content originale11.txt -Raw) (Get-Content recuperato11.txt -Raw)

Controllo finale:

- volume origine e volume ripristinato devono avere ID diversi;
- entrambi devono essere `available`;
- zona, dimensione e cifratura devono essere coerenti;
- backup e restore devono essere `COMPLETED`;
- hash locali uguali indicano che il metodo di confronto e' corretto, non che il volume AWS contenga file applicativi.

## Output atteso
`consegna11.md`: registro dei processi reali o simulati, confronto dei volumi, hash locali, limiti e stato finale delle risorse.

## Checkpoint
Due ID di volume distinti. Nessun dato applicativo dichiarato recuperato. Processo completato e servizio funzionante sono risultati differenti.

## Troubleshooting rapido
Ruolo non utilizzabile: non modificarlo, passa al caso locale. Processo in corso: non considerarlo riuscito. Restore negato: verifica tipo, zona e dimensione consentiti; conserva il messaggio anonimizzato e avvia la pulizia possibile.

## Cleanup obbligatorio
Quando i processi sono terminati, elimina il volume ripristinato e quello originale, entrambi non collegati. Elimina il punto di ripristino tramite AWS Backup e attendi la sua rimozione; poi elimina il vault vuoto. Non cancellare chiavi gestite AWS. Per processi ancora attivi, interrompi il backup se l'interfaccia lo consente; verifica successivamente eventuali volumi e punti prodotti e rimuovili. Registra esplicitamente ogni risorsa ancora presente: chiudere Academy non costituisce cleanup. Elimina i tre file locali dopo la consegna.
