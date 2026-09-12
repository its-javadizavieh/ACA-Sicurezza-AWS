# Lab 01 - Fondamenti di sicurezza

## Obiettivo
Assegnare responsabilità e progettare controlli verificabili per rapporti industriali sintetici.

## Prerequisiti
Editor Markdown. Nessuna risorsa AWS necessaria; la console è facoltativa.

## Scenario
Un tecnico carica rapporti in S3, un analista li legge e un responsabile ne autorizza la cancellazione. Un'applicazione EC2 elabora i rapporti. Una credenziale condivisa rende difficile attribuire le operazioni.

## Step (numerati)
1. Crea `consegna01.md` con colonne componente, controllo, responsabile, evidenza e intervento.
2. Compila otto righe: edifici, host fisico, guest OS, libreria applicativa, accesso ai rapporti, classificazione dati, configurazione rete e credenziali di sessione.
3. Disegna il percorso tecnico -> archivio -> analista e indica due confini di fiducia.
4. Proponi tre interventi contro divulgazione, cancellazione e mancata attribuzione. Per ciascuno specifica se previene, rileva o permette il recupero.
5. Scrivi una prova positiva e una negativa per intervento: identità, operazione, oggetto e risultato atteso.
6. Scambia la consegna con un collega e correggi due responsabilità o prove ambigue. Simula su carta la perdita della credenziale del lettore e valuta l'impatto residuo.

## Output atteso
Otto assegnazioni, diagramma dei dati, tre controlli e sei prove osservabili.

## Checkpoint
Il cliente aggiorna guest OS e librerie. Il lettore non cancella. Un avviso successivo a una cancellazione non viene classificato come blocco preventivo.

## Troubleshooting rapido
Se scrivi “responsabilità condivisa”, separa le componenti. Se una prova dice “sicuro”, sostituiscilo con un risultato misurabile. Tutta l'attività è eseguibile localmente anche senza console.

## Cleanup obbligatorio
Nessuna risorsa creata. Chiudi eventuali sessioni, elimina note contenenti credenziali e conserva solo la consegna sintetica.
