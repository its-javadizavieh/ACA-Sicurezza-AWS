# Lab 06 - VPC e filtri di rete

## Obiettivo
Configurare una rete isolata e verificare la logica dei filtri senza avviare istanze o gateway a pagamento.

## Prerequisiti
Console VPC, sessione Academy e regione `us-east-1`. Usa il prefisso `sicurezza06-SUFFISSO`.

## Scenario
Una subnet server riceverebbe HTTPS da un client aziendale `10.20.1.10`. L'attività configura gli oggetti; non realizza il collegamento con quella rete sorgente.

## Step (numerati)

1. VPC -> Your VPCs -> Create VPC.
2. Selezionare `VPC only`.
3. Nome: `sicurezza06-SUFFISSO`.
4. IPv4 CIDR: `10.60.0.0/16`.
5. IPv6: none.
6. Creare la VPC.
7. VPC -> Subnets -> Create subnet.
8. Selezionare la VPC del lab.
9. CIDR subnet: `10.60.1.0/24`.
10. Scegliere una AZ in `us-east-1`.
11. Lasciare disabilitata l'assegnazione automatica IPv4 pubblico.
12. VPC -> Security Groups -> Create security group.
13. Selezionare la VPC del lab.
14. Aggiungere inbound rule: HTTPS, TCP 443, source `10.20.1.0/24`.
15. Rimuovere la regola outbound generale solo dal gruppo creato.
16. VPC -> Network ACLs -> Create network ACL.
17. Selezionare la VPC del lab.
18. Aggiungere inbound rule 100: TCP 443, source `10.20.1.0/24`, Allow.
19. Aggiungere outbound rule 100: TCP 1024-65535, destination `10.20.1.0/24`, Allow.
20. Network ACL -> Subnet associations -> Edit -> associare solo la subnet del lab.
21. Registrare la ACL precedente per il ripristino.

## Svolgimento guidato

Configurazione corretta:

- VPC: `sicurezza06-SUFFISSO`, CIDR `10.60.0.0/16`;
- subnet: CIDR `10.60.1.0/24`, una AZ in `us-east-1`, IPv4 pubblico automatico disabilitato;
- Security Group: inbound HTTPS TCP 443 da `10.20.1.0/24`;
- Security Group: outbound generale rimosso solo dal gruppo creato nel lab;
- Network ACL: inbound rule 100 TCP 443 da `10.20.1.0/24`, Allow;
- Network ACL: outbound rule 100 TCP 1024-65535 verso `10.20.1.0/24`, Allow;
- associazione ACL: solo subnet del lab, annotando la ACL precedente per il ripristino.

Decisioni attese sui pacchetti:

- ingresso `10.20.1.10` -> TCP 443: ammesso;
- ingresso `10.20.1.10` -> TCP 22: negato;
- uscita verso `10.20.1.10:55000`: ammessa;
- uscita verso `10.20.1.10:80`: negata.

Nota operativa:

- nessun comando AWS necessario, perche la soluzione e' piu chiara dalla console VPC.

## Output atteso
`consegna06.md`: schema, identificativi delle sole risorse create, regole, decisioni e prova del cleanup.

## Checkpoint
Risultati ACL: ammesso, negato, ammesso, negato. La VPC non ha accesso Internet e il gruppo non è collegato a server: nessun test applicativo viene dichiarato riuscito.

## Troubleshooting rapido
Associazione errata: controlla VPC e subnet selezionate. Eliminazione negata per dipendenze: verifica interfacce e associazioni senza cancellare risorse altrui. Accesso negato: percorso locale.

## Cleanup obbligatorio
Riassocia alla subnet l'ACL precedente. Elimina ACL e gruppo creati, poi subnet e VPC dedicate. Gli oggetti predefiniti della VPC vengono gestiti insieme alla VPC. Verifica che non restino le risorse registrate; non avendo creato NAT, istanze o indirizzi pubblici non devono comparire tali costi. Se restano dipendenze, annotale per il docente.
