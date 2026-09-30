# Lab 03 - Compliance, KMS e identità

## Obiettivo
Distinguere attendibilità e permessi, quindi documentare uso e dismissione di una chiave KMS.

## Prerequisiti
Sessione temporanea e CLI, regione `us-east-1`. Non servono dati reali.

## Scenario
Il requisito didattico è “conservare il rapporto cifrato e identificare il soggetto che può usare la chiave”. Non costituisce una certificazione di conformità.

## Step (numerati)

1. Aprire IAM -> Roles e consultare il ruolo disponibile per il laboratorio, se visibile.
2. Leggere la trust policy senza modificarla. Se il ruolo non e' visibile, usare il caso guidato del lab.
3. Scrivere in consegna: la trust policy dice chi puo assumere un ruolo; i permessi dicono cosa puo fare.
4. Aprire KMS -> Customer managed keys -> Create key.
5. Key type: symmetric.
6. Key usage: encrypt and decrypt.
7. Alias o descrizione: `sicurezza03-SUFFISSO`.
8. Lasciare impostazioni sicure predefinite; non creare policy restrittive manuali.
9. Creare la chiave e annotare `Key ID`.
10. Usare CLI per cifrare e decifrare il testo sintetico, perche la prova e' piu chiara da terminale.
11. Annotare che il confronto tra file originale e decifrato e' riuscito.
12. Scrivere requisito -> controllo -> evidenza -> limite.
13. Tornare in KMS -> chiave creata -> Key actions -> Schedule key deletion.
14. Impostare finestra a 7 giorni.

## Svolgimento guidato

Risposte da inserire nella consegna:

- trust policy: definisce chi puo assumere un ruolo;
- policy di permesso: definisce quali azioni sono consentite;
- requisito: proteggere un dato sintetico;
- controllo: cifratura con chiave KMS customer managed;
- evidenza: testo decifrato uguale al testo originale;
- limite: la cifratura non dimostra da sola che l'accesso IAM sia corretto.

Configurazione KMS corretta:

- regione: `us-east-1`;
- tipo chiave: symmetric;
- uso: encrypt and decrypt;
- alias o descrizione: `sicurezza03-SUFFISSO`;
- policy: impostazioni sicure predefinite, senza policy manuali restrittive.

Prova da terminale del ciclo cifratura e decifratura. Usare il `KEY_ID` della chiave creata nel lab.

Ubuntu:

    printf '%s' 'rapporto sintetico 03' > chiaro03.txt

    aws kms encrypt --key-id KEY_ID --plaintext fileb://chiaro03.txt --region us-east-1 --query CiphertextBlob --output text > cifrato03.b64

    base64 -d cifrato03.b64 > cifrato03.bin

    aws kms decrypt --ciphertext-blob fileb://cifrato03.bin --region us-east-1 --query Plaintext --output text > decifrato03.b64

    base64 -d decifrato03.b64 > decifrato03.txt

    cmp chiaro03.txt decifrato03.txt

macOS:

    printf '%s' 'rapporto sintetico 03' > chiaro03.txt

    aws kms encrypt --key-id KEY_ID --plaintext fileb://chiaro03.txt --region us-east-1 --query CiphertextBlob --output text > cifrato03.b64

    base64 -D -i cifrato03.b64 -o cifrato03.bin

    aws kms decrypt --ciphertext-blob fileb://cifrato03.bin --region us-east-1 --query Plaintext --output text > decifrato03.b64

    base64 -D -i decifrato03.b64 -o decifrato03.txt

    cmp chiaro03.txt decifrato03.txt

Windows PowerShell:

    Set-Content -Path chiaro03.txt -Value "rapporto sintetico 03" -NoNewline

    aws kms encrypt --key-id KEY_ID --plaintext fileb://chiaro03.txt --region us-east-1 --query CiphertextBlob --output text > cifrato03.b64

    [IO.File]::WriteAllBytes("cifrato03.bin", [Convert]::FromBase64String((Get-Content cifrato03.b64 -Raw)))

    aws kms decrypt --ciphertext-blob fileb://cifrato03.bin --region us-east-1 --query Plaintext --output text > decifrato03.b64

    [IO.File]::WriteAllBytes("decifrato03.txt", [Convert]::FromBase64String((Get-Content decifrato03.b64 -Raw)))
    
    Compare-Object (Get-Content chiaro03.txt -Raw) (Get-Content decifrato03.txt -Raw)

Controllo finale:

- il confronto non deve mostrare differenze;
- la chiave deve essere pianificata per eliminazione a 7 giorni;
- i file locali `chiaro03.txt`, `cifrato03.b64`, `cifrato03.bin`, `decifrato03.b64`, `decifrato03.txt` vanno eliminati quando non servono piu.

## Output atteso
`consegna03.md` con distinzione tra policy, prova di integrità, catena di evidenze e stato finale della chiave.

## Checkpoint
Il testo decifrato coincide con l'originale; una chiave in cancellazione pianificata non è utilizzabile per le operazioni crittografiche.

## Troubleshooting rapido
Token scaduto: rinnova dal portale. Base64 non valido: controlla che il file contenga soltanto il campo richiesto. `AccessDenied`: usa il caso locale.

## Cleanup obbligatorio
Solo per la chiave creata: in KMS scegli la chiave del lab e pianifica la cancellazione a 7 giorni. Verifica stato `PendingDeletion` e data. Non dichiararla già eliminata. Elimina i file locali generati durante la prova. Se la pianificazione fallisce, registra l'identificativo e segnala al docente la risorsa ancora presente.
