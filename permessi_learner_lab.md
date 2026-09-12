# Permessi e risorse comuni nel Learner Lab

Questo documento riassume le regole operative usate nei laboratori del corso. Gli studenti non devono leggere o modificare file interni dell'ambiente AWS Academy: i vincoli sono gia considerati nelle tracce dei lab e nelle soluzioni.

## Accesso e risorse comuni

Durante i laboratori si usa una sessione temporanea di AWS Academy Learner Lab.

Regole pratiche:

- lavorare nella regione indicata dal lab, normalmente `us-east-1`;
- creare solo le risorse richieste dal laboratorio corrente;
- usare nomi con prefisso `sicurezzaNN-SUFFISSO`;
- usare solo dati sintetici;
- non salvare credenziali nelle consegne;
- non modificare ruoli, policy, profili, log, trail, recorder o risorse condivise;
- completare il cleanup prima di terminare la sessione.

## Scelte per i laboratori

Le attivita sono progettate per restare compatibili con il Learner Lab.

- S3: bucket privati, oggetti sintetici e crittografia SSE-S3.
- KMS: chiavi dedicate al lab, dati sintetici e cancellazione pianificata.
- IAM: lettura o simulazione di policy, senza creare utenti o modificare ruoli condivisi.
- MFA: analisi di condizioni IAM, senza modificare il login Academy.
- SCP e Access Analyzer: casi locali quando il servizio non e' disponibile agli studenti.
- VPC: VPC, subnet, Security Group e Network ACL dedicati, senza istanze o gateway non richiesti.
- VPN e Direct Connect: progettazione e analisi, senza creare collegamenti a pagamento.
- CloudTrail: lettura della cronologia eventi, senza creare o cancellare trail.
- CloudWatch: metriche e allarmi dedicati, con cleanup dell'allarme.
- AWS Config: lettura o progetto guidato, senza modificare recorder condivisi.
- WAF: Web ACL regionale dedicata, senza associazioni a risorse reali se non richiesto.
- Shield: discussione della protezione e delle scelte operative, senza attivare sottoscrizioni.
- Secrets Manager: segreti sintetici, versioni e cancellazione pianificata.
- GuardDuty: sample finding solo su detector creato dallo studente; detector condivisi non vanno modificati.
- Inspector: analisi di vulnerabilita sintetiche quando l'attivazione non e' disponibile.
- AWS Backup: piccoli volumi EBS dedicati, vault dedicato, recovery point e cleanup.

## Limiti da rispettare

- Non tentare di aggirare `AccessDenied` cambiando ruolo o policy.
- Non creare risorse fuori regione se il lab richiede `us-east-1`.
- Non creare risorse costose o non richieste, come NAT Gateway, istanze non previste, sottoscrizioni o test di carico.
- Non usare Object Lock, retention legale o blocchi irreversibili.
- Non usare dati reali, password reali, account ID completi o informazioni personali negli screenshot.
- Non considerare conclusa la pulizia solo perche la sessione Academy viene chiusa.

## Cosa fare se compare AccessDenied

Se compare `AccessDenied`:

1. controllare regione e nome della risorsa;
2. verificare di lavorare solo su risorse create per il lab;
3. annotare il messaggio senza dati sensibili;
4. usare il fallback simulato indicato nel lab;
5. non modificare ruoli, policy o risorse condivise.

## Riferimenti tecnici

- AWS Shared Responsibility Model: https://aws.amazon.com/compliance/shared-responsibility-model/
- AWS IAM policy evaluation logic: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html
- Amazon S3 server-side encryption: https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html
- AWS KMS key deletion: https://docs.aws.amazon.com/kms/latest/developerguide/deleting-keys.html
- Amazon VPC security groups: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html
- Amazon VPC network ACLs: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html
- AWS CloudTrail event history: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html
- Amazon CloudWatch alarms: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html
- AWS Config managed rules: https://docs.aws.amazon.com/config/latest/developerguide/managed-rules-by-aws-config.html
- AWS WAF web ACLs: https://docs.aws.amazon.com/waf/latest/developerguide/web-acl.html
- AWS Secrets Manager versions: https://docs.aws.amazon.com/secretsmanager/latest/userguide/getting-started.html
- Amazon GuardDuty sample findings: https://docs.aws.amazon.com/guardduty/latest/ug/sample_findings.html
- AWS Backup recovery points: https://docs.aws.amazon.com/aws-backup/latest/devguide/recovery-points.html
