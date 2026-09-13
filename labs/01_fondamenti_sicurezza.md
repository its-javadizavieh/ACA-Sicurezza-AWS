# Lab 01 - Fondamenti di sicurezza

## Obiettivo
Assegnare responsabilità e progettare controlli verificabili per rapporti industriali sintetici.

## Prerequisiti
Editor Markdown. Nessuna risorsa AWS necessaria; la console è facoltativa.

## Scenario
Un tecnico carica rapporti in S3, un analista li legge e un responsabile ne autorizza la cancellazione. Un'applicazione EC2 elabora i rapporti. Una credenziale condivisa rende difficile attribuire le operazioni.

## Step (numerati)

1. Creare `consegna01.md`.
2. Aggiungere una sezione `Scenario` con tecnico, analista, responsabile, S3 ed EC2.
3. Aggiungere una sezione `Responsabilita`.
4. Compilare otto elementi:
   - edifici: AWS;
   - host fisico: AWS;
   - guest OS EC2: cliente;
   - libreria applicativa: cliente;
   - accesso ai rapporti: cliente;
   - classificazione dati: cliente;
   - configurazione rete: cliente;
   - credenziali/sessioni: cliente.
5. Disegnare o descrivere il flusso: tecnico -> archivio S3 -> analista.
6. Indicare due confini di fiducia: identita verso AWS, applicazione verso S3.
7. Scrivere tre controlli:
   - accesso minimo a S3;
   - cancellazione consentita solo al responsabile;
   - tracciamento con identita/sessione distinguibile.
8. Scrivere sei prove: positiva e negativa per lettura, cancellazione e attribuzione.
9. Scambiare la consegna con un collega e correggere due responsabilita o prove ambigue. Simulare su carta la perdita della credenziale del lettore e valutare l'impatto residuo.

## Svolgimento guidato

Valori da inserire in `consegna01.md`:

- edifici: responsabile AWS, controllo fisico e ambientale, evidenza da documentazione AWS;
- host fisico: responsabile AWS, controllo su hardware e virtualizzazione fisica, evidenza da modello di responsabilita condivisa;
- guest OS EC2: responsabile cliente, patching e hardening del sistema operativo, evidenza da piano aggiornamenti;
- libreria applicativa: responsabile cliente, aggiornamento dipendenze, evidenza da versione installata o changelog;
- accesso ai rapporti: responsabile cliente, permessi minimi su S3, evidenza da test di lettura consentita e cancellazione negata;
- classificazione dati: responsabile cliente, etichetta dati sintetici o riservati, evidenza nella consegna;
- configurazione rete: responsabile cliente, regole coerenti con il flusso tecnico -> archivio -> analista, evidenza da diagramma;
- credenziali/sessioni: responsabile cliente, identita distinguibili, evidenza da log attribuibile.

Controlli da proporre:

- divulgazione: lettura S3 limitata ai soli prefissi necessari;
- cancellazione: `DeleteObject` consentito solo al responsabile;
- mancata attribuzione: niente credenziali condivise e tracciamento per identita o sessione.

Prove minime:

- lettore autorizzato legge un rapporto previsto;
- lettore non autorizzato non legge fuori perimetro;
- responsabile cancella un oggetto di test;
- analista non cancella lo stesso oggetto;
- evento/log mostra l'identita che ha eseguito l'operazione;
- uso di credenziale condivisa viene classificato come controllo non accettabile.

## Output atteso
Otto assegnazioni, diagramma dei dati, tre controlli e sei prove osservabili.

## Checkpoint
Il cliente aggiorna guest OS e librerie. Il lettore non cancella. Un avviso successivo a una cancellazione non viene classificato come blocco preventivo.

## Troubleshooting rapido
Se scrivi “responsabilità condivisa”, separa le componenti. Se una prova dice “sicuro”, sostituiscilo con un risultato misurabile. Tutta l'attività è eseguibile localmente anche senza console.

## Cleanup obbligatorio
Nessuna risorsa creata. Chiudi eventuali sessioni, elimina note contenenti credenziali e conserva solo la consegna sintetica.
