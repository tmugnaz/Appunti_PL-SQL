# PL/SQL
---
---
## INTRODUZIONE 
---
PL sta per Procedural Language ed è quindi un linguaggio PROCEDURALE per applicazioni flessibili che garantisce strumenti per la gestione,manipolazione e analisi dei dati in un ecosistema Oracle DB

È un linguaggio di ALTO LIVELLO che permette la creazione di: 
* funzioni
* trigger 
* Stored Procedure 
* Controllo del Flusso del codice ( if else )
* Gestione degli errori che possono risultare da blocchi di codice  

Consente la creazione di Tipi di dati personalizzati
Il linguaggio PL/SQL serve a sviluppare procedure e funzioni **SOLO** in un DB Oracle ed è necessario per migliorare le prestazioni del DB.

---
## LEZIONE 1 : BASI DI SQL , BLOCCHI E VARIABILI
---
(SEZIONE 4)

---
### BLOCCHI DI CODICE 

Servono a  raggruppare istruzioni e organizzare la logica del codice
Ne esistono di 4 tipi :
* *ANONIMI* → Non hanno nome e vengono eseguiti solo una volta; non      vengono salvati nel dbms ma è possibile salvarli in file per riutilizzarli
* *PROCEDURE* → Viene eseguito ogni volta che viene chiamato nel codice principale 
* *FUNZIONI* → Come le Procedure ma hanno un output 
* *PACHETTI* → Sono Gruppi di Procedure e Funzioni correlate.

I Blocchi sono divisi in SEZIONI 
Ogni Sezione ha questa sintassi:
<center> DICHIARAZIONE </center>
  <center>            ⬇</center>
<center>ESECUZIONE</center>
   <center>            ⬇</center>
<center>TERMINE </center>

Nell Foglio di lavoro in SQL tutto ciò si traduce in 

```sql   
DECLARE 
--Dichiarazione delle variabili
BEGIN 
--inizio procedure e modifica variabili
END;
-- fine del blocco
/* commento
    multi 
    riga */
```
---
### VARIABILI PL/SQL 
Come in tutti i linguaggi di programmazione le variabili sono parte centrale dell'esecuzione di codice, sono utilizzate per memorizzare e manipolare i dati all' interno del programma.

PL/SQL è strettamente tipato ovvero durante la dichiarazione delle variabili è necessario specificare il tipo di dato.

i tipi delle variabili sono quelli dei dati delle tabel in SQL ovvero:

| Tipo di Dato       | Descrizione                                       | Esempio                  |
|--------------------|---------------------------------------------------|--------------------------|
| CHAR(n)            | Stringa di lunghezza fissa fino a n caratteri     | CHAR(10)                 |
| VARCHAR(n)         | Stringa di lunghezza variabile fino a n caratteri | VARCHAR(255)             |
| NCHAR(n)           | Stringa Unicode di lunghezza fissa fino a n caratteri | NCHAR(10)              |
| NVARCHAR(n)        | Stringa Unicode di lunghezza variabile fino a n caratteri | NVARCHAR(255)         |
| NUMBER(p, d)       | Numero a virgola mobile con precisione p e scala d | NUMBER(10, 2)            |
| FLOAT              | Numero in virgola mobile a precisione doppia      | FLOAT                    |
| BINARY_FLOAT       | Numero in virgola mobile a precisione singola     | BINARY_FLOAT             |
| BINARY_DOUBLE      | Numero in virgola mobile a precisione doppia      | BINARY_DOUBLE            |
| DATE               | Data (senza ora)                                  | '2024-04-13'             |
| TIMESTAMP          | Data e ora con fuso orario                        | '2024-04-13 12:34:56'    |
| CLOB               | Dati di grandi dimensioni di tipo carattere       | CLOB                     |
| BLOB               | Dati di grandi dimensioni di tipo binario         | BLOB                     |
| BFILE              | Riferimento a un file binario esterno             | BFILE                    |


Passiamo adesso alla dichiarazione di variabili in PL/SQL, segue sempre la sitassi 
NOME TIPODATO;
ovvero

```sql
P_NomeVariabile TIPODATO;
--Esempio 
P_NomeCliente VARCHAR(10);
```  
È Possibile dichiarare e inizializzare le variabili insieme :

```sql
P_NomeCliente VARCHAR(10):= 'GIORGIO';

```  
È anche possibile eseguire quella che viene definita procedura di **ANCHORING** 

```sql
P_NomeCliente CLIENTE.NOME%TYPE;
-- o anche con inizializzazione 
P_COgnomeCliente CLIENTE.COGNOME%TYPE:='ROSSI';

```  
L'ANCHORING permette quindi di dichiarare una variabile con il tipo di dato corrispondente al dato specificato dal %TYPE ovvero lo stesso tipo di dato di quel campo in quella tabella seguendo la sintassi **TABELLA.COLONNA%TYPE** in modo da avere corrispondenza tra i due e facilitare sia inserimento che assegnazione.

Un altro tipo di dichiarazione dato è quella dove l'**UTENTE** assegna direttamente il valore a quella variabile tramite l'utilizzo del simbolo & seguito dal nome che vogliamo far comparire a schermo:


```sql
DECLARE 
P_NomeNuovoCliente CLIENTE.NOME%TYPE := '&Nome';
```  
Questo risulterà in un prompt all'avvio del blocco di codice dove l'utente avrà modo di inserire il nome scelto. Una volta fatto ciò per tutta la durata dell'esecuzione del codice quella variabile avrà quel valore.

![alt text](IMGS/image.png)
 
se nel blocco inseriamo anche il comando 


```sql
BEGIN 
dbms_output.put_line('il nome del cliente è: ' || p_NomeNuovoCliente);
END;
```  
ci troveremo nell'output DBMS proprio il nome che abbiamo scritto 
![alt text](IMGS/image-1.png)

![alt text](IMGS/image-2.png)

---
### MANIPOLAZIONE DELLE VARIABILI

Come in ogni linguaggio la manipolazione delle variabili è essenziale.
Anche il PL/SQL è possibile manipolare le variabili ad esempio:
* è possibile usare funzioni matematiche sia nella dichiarazione delle stesse che nel blocco delle procedure 

```sql
P_TotaleAcquisti = SUM(P_ACQUISTI * 3);
```  

* è possibile assegnare valori da dati presi direttamente tramite una QUERY 
```sql
DECLARE 
  P_NOME CLIENTI.NOME%TYPE;
BEGIN 
  SELECT NOME INTO P_NOME FROM CLIENTI WHERE NOME = 'GIORGIO';
```  
ovviamente le condizioni del where possono cambiare andando a prendere informazioni piu complesse.

* è possibile inserire direttamente le variabili ( magari rielaborate nelle procedure ) nelle tabelle come nuove entità


```sql
INSERT INTO CLIENTE (NOME,COGNOME) VALUES (P_NOME,P_COGNOME);
```  
Queste inserzioni saranno poi approfondite, in particolare il concetto di conferma delle modifiche.

---
### BLOCCHI ANNIDIATI E SCOPE DELLE VARIABILI

I Blocchi Annidiati non sono altro che blocchi dentro la parte "*BEGIN*" di un altro blocco

```sql
DECLARE 
BEGIN
-- blocco annidiato 
  DECLARE
  BEGIN
  END --fine blocco annidiato, riprende il blocco principale
END   
```  
Le variabili dichiarate nel blocco principale saranno visibili al blocco annidiato ma non è vero il contrario

```sql
DECLARE 
  VAR NUMBER :=6;
BEGIN 
  DECLARE 
    VAR2 NUMBER :=3;
  BEGIN
    dbms_output.put_line(VAR + VAR 2 = || VAR+ VAR2); 
    -- (6+3) e stamperà a video il risultato
  END 
  dbms_output.put_line(VAR + VAR 2 = || VAR+ VAR2); 
  -- darà errore , VAR2 non esiste e non è stata neanche dichiarata per il blocco principale
  END;
```  
È anche possibile nominare variabili nello stesso modo sia nel blocco interno che in quello esterno ma in quel caso i valori di assegnazione avranno preceenza quelli del blocco interno
```sql
DECLARE 
VAR NUMBER :=1;
BEGIN 
  DECLARE
  VAR NUMBER :=2;
  BEGIN
  dbms_output.put_line(VAR = || VAR); 
  -- darà come risultato '2'
  END
dbms_output.put_line(VAR = || VAR); 
-- darà come risultato '1'
END;  
```  
Per evitare confusioni è possibile usare le **ETICHETTE**

```sql
<<PRIMARIO>>
DECLARE 
BEGIN 
  <<SECONDARIO>>
  DECLARE 
  BEGIN 
  END
END; 
``` 
in questo caso il chiamare le variabili saraà analogo al chiamare un campo in una tabella :
 
```sql
primario.VAR1 ;
secondario.VAR2;
```  
Ovviamente l'etichetta del blocco annidiato **NON** esisterà per il blocco Principale.

---
---
## LEZIONE 2 : GESTIONE DELLE TRANSAZIONI
---
(SEZIONE 5)

---
Nel caso di errori durante l'esecuzione di blocchi di codice è necessario poter tornare indietro : per questo esiste il comando **ROLLBACK**
che permette di riportare il db all'ultimo salvataggio , scartando così le modifiche fatte in precedenza 
```sql
ROLLBACK;
```
Ma cosa si intende per modifica?

Ogni comando appartenente alla famiglia DML (Data Manipulation System) è considerato una modifica al DB (i metodi CRUD)
e ogni tipo di modifica è considerato **SOTTO TRANSAZIONE** ovvero finche il comando non viene accettato del tutto i cambiamenti sono salvati in una __memoria temporanea__ per preservare l'integrità del DB.

Non è infatti l'utente base a decidere ma la logica del programma decide se accettare o scartare le modifiche fatte dall'utente.

Gli utenti esterni infatti saranno in grado di modificare il db e i suoi dati ma non riusciranno a vedere le modifiche che hanno apportato , non finchè la transazione non sarà chiusa e il risultato confermato o ribaltato 

```sql
COMMIT;
```
Comando per accettare tutti i cambiamenti , proprio come in GIT;

IL COMMIT è PERMANENTE

```sql
SAVEPOINT a ;
```
È un checkpoint che salva le modifiche (NON è UN COMMIT), e serve come punto di riferimento per i successivi commit o rollback;

Il rollback infatti puo referenziare un savepoint scartando tutte le modifiche successive al savepoint
<center>OPERAZIONE</center>
<center> ⬇</center>
<center>SAVEPOINT 'X'</center>
<center>⬇</center>
<center>MORE WORK</center>
<center> ⬇</center>
<center>COMMIT OR ROLLBACK TO SAVEPOINT 'X'</center>

---
---
## LEZIONE 3 : ESPRESSIONI CONDIZIONALI
---
(SEZIONE 6)

---
Come in tutti i linguaggi di programmazione le espressioni condizionali sono una parte fondamentale del flusso del codice per eseguire determinate azioni quando una condizione è vera e altre azioni quando è falsa.

In PL/SQL sono :

* **IF THEN ELSE** 
  
  questa la sintassi:

```sql
IF (condizione) THEN Azione1
ELSE IF (condizione2) THEN Azione2
ELSE  (Condizione implicita) 
      Azione 3
END IF;      
``` 
* **CASE** 

  Viene utilizzato per selezionare opzioni diverse in base ad un valore specifico (il SELETTORE)


```sql
CASE (Selettore)
WHEN (opzione1) THEN (azione1)
WHEN (opzione2) THEN (azione2)
ELSE (azione di base)
END CASE;
```
* COALESCE usato per restituire il *Primo* valore NON nullo di una lista di valori 
```sql
COALESCE (V1,V2,V3...Vn);
```
* SEARCHED CASE espressione avanzata del CASE per valutare più condizioni contemporaneamente non c'è quindi bisogno del selettore perla clausola CASE
```sql
CASE 
WHEN (condizione1)THEN azione1
WHEN (condizione2)THEN azione2
WHEN (condizione3)THEN azione3
END CASE;
```
* NULLIF  restituisce NULL se due valori sono uguali e altrimenti restituisce il PRIMO Valore 
```sql
NULLIF (V1,V2)
```
* NVL serve per "riempire" il valore nullo in una colonne (se trovato) con un valore da noi stabilito. Serve per eseguire funzioni matematiche su colonne che sappiamo potrebbero avere valori nulli.
```sql
NVL(SPESA,0);
```
È Anche possibile avere più condizioni una annidiata all'interno dell'altra 

```sql
IF condizione THEN 
      IF condizione THEN azione
      ELSIF condizione THEN azione
      ELSE azione
      END IF;
ELSIF condizione 
ELSE azione 
END IF;      
```
---
---
## LEZIONE 4 : I CICLI
---
(SEIONE 7)

---
Anche i Cicli sono parte essenziale del Controllo del Flusso e in PL/SQL si possono trovare diverse tipologie di ciclo:

* **WHILE** :
  
  Check su una condizione e inizio del loop
```sql
WHILE (condizione) 
LOOP
Azione1
Azione2
Azione3
END LOOP;
```
* **FOR** 
  
  Contatore viene definito tra due valori : da dove parte (inf) e fin dove deve arrivare (sup)con nel mezzo ai due valori 2 punti (..).
  
```sql
FOR i IN 1..X
LOOP
  Azione che si ripeterà X volte 
END LOOP;  
```  
* **LOOP SEMPLICE**

  il più semplice dei cicli , IMPORTANTE specificare la condizione di uscita

```sql
LOOP
azione1
azione2
EXIT WHEN condizione
END LOOP;
```
---
Il Comando CONTINUE è utilizzato all'interno dei cicli per interrompere l'iterazione corrente e passare alla successiva ;
```sql
DECLARE
    v_counter NUMBER := 0; --contatore
BEGIN
    FOR i IN 1..5 LOOP
        IF MOD(i, 2) = 0 THEN
            -- Se i è pari, salta questa iterazione
            CONTINUE;
        END IF;
        v_counter := v_counter + 1;
    END LOOP;
    --possiamo vedere che la variabile contatore è aumentata SOLO per i numeri dispari
    DBMS_OUTPUT.PUT_LINE('Iterazione ' || i || ': Contatore = ' || v_counter)
END;
```
Molto utile è anche il comando EXIT che serve per Uscire completamente da un ciclo data una certa condizione: usato molto spesso con un IF all'interno del ciclo, la sintassi è uguale a quella del CONTINUE.

---
---
## LEZIONE 5: GESTIONE DELLE ECCEZIONI
---
(SEZIONE 8)

---
### TIPI DI ECCEZIONI

Nel PL/SQL è importante gestire le Eccezioni sopratutto nei contesti dove stiamo modificando dei dati e c'è il rischio che il processo venga interrotto da una o più eccezioni.

Le Eccezioni che possiamo trovare sono di due tipologie 

- Eccezioni Predefinite 
- Eccezioni Definite dall'Utente

Le Predefinite sono Eccezioni di base se così possiamo dire, errori comuni relativi a operazioni con i dati :

* `NO_DATA_FOUND`: Quando una query non trova alcun dato.
* `TOO_MANY_ROWS`: Quando una query restituisce più di una riga per un'operazione di singola riga.
* `ZERO_DIVIDE`: Quando si tenta di dividere per zero. 

Ogni qualvolta pensiamo di aver a che fare con un operazione che potrebbe lanciare un eccezione è necessario gestirla del blocco `EXCEPTION`:

```sql
EXCEPTION 
    WHEN NO_DATA_FOUND THEN 
    DBMS_OUTPUT.PUT_LINE('Nessun dato trovato.');
END;
```
Non è considerata buona norma cercare di gestire queste eccezioni di base tramite il "CASE": 
`WHEN OTHERS THEN` che lancia l'azione descritta quando incorre in una o più eccezioni senza specificarne il tipo.

---

### ECCEZIONI DEFINITE DALL'UTENTE

Sono le eccezioni principali con cui andremo a lavorare : possiamo creare un eccezione per qualsiasi tipologia di caso o errore e gestirla in maniera opportuna.  
Possiamo considerare il blocco BEGIN END come un TRY  e il EXCEPTION WHEN come il nostro CATCH

```sql
BEGIN 
--codice che può generare errori
EXCEPTION 
-- gestione degli errori
END ;
```
Possiamo generare noi un messaggiodi errore tramite la clausola RAISE 
es:
```sql
EXCEPTION 
WHEN NO_DATA_FOUND THEN 
RAISE_EXCEPTION_ERROR(-20000,'ERRORE')
```
in questo caso nel dump di errore visualizzeremo il codice e il messaggio, i codici personalizzati dovranno sempre avere un valore compreso tra -20k e -20999
```sql
DECLARE
   v_num NUMBER := 0;
BEGIN
   -- Tentativo di accesso a una tabella inesistente
   SELECT col INTO v_num FROM non_existent_table;
EXCEPTION
   WHEN OTHERS THEN
      DBMS_OUTPUT.PUT_LINE('Errore: ' || SQLERRM);
END;
```

Il comando SQLCODE è una funzione che fornisce il codice di stato dell'ultima operazione (solitamente l'errore)
mentre  SQLERRM restituisce il messaggio di errore completo associato all'errore corrente 

Entrambi questi comandi servono per fornire  informazioni piu dettagliate per migliorare la diagnosi dell'errore.
#### PROPAGAZIONE DEGLI ERRORI
Nei blocchi annidiati è possibile gestiglie di errori del blocco secondario SIA all'interno dello stesso sia all'interno di quello primario poichè la visibilità della gestione è uguale a quella delle variabili dichiarate nei blocchi annidiati

---

#### DICHIARAZIONE E GESTIONE
Possiamo Dichiarare le eccezioni proprio come se fossero variabili

```sql
DECLARE
   my_exception EXCEPTION;
BEGIN
   IF condition THEN
      RAISE my_exception;
   END IF;
EXCEPTION
   WHEN my_exception THEN
      DBMS_OUTPUT.PUT_LINE('Eccezione generata.');
END;

```
Posso anche utilizzare il RAISE_EXCEPTION_ERROR(codiceErrore,messaggio)
ma se gestito nel "EXCEPTION WHEN OTHERS" necessiterò di stampare a schermo SQLERRM per visualizzare il codice

**COMANDO PRAGMA**
Possiamo sovrascrivere le eccezioni di base con codici e messaggi personalizzati tramite il comando pragma:

```sql
DECLARE
E_IdInvalido EXCEPTION;

PRAGMA EXCEPTION_INIT(E_IdInvalido,-20001);
```
in questo caso quando andremo a incontrare la possibilità che il codice lanci l'eccezione che vogliamo gestire allora possiamo 
```sql
IF condizione che puo generare errore THEN 
-- in questo caso NO_DATA_FOUND
RAISE_APPLICATION_ERROR (-20001, 'id non valido');
```
e quando andremo a gestire gli errori possiamo 
```sql
WHEN E_IdInvalido 
    THEN
       DBMS_OUTPUT.PUT_LINE (SQLERRM);
       DBMS_OUTPUT.PUT_LINE (SQLCODE);
```
Ovvero se incontreremo quell'errore abbiamo la possibilità di aggiungere informazioni per rendere la diagnosi più facile.
È utile pe rgestire errori generici NON tramite il EXCEPTION WHEN OTHERS

---
---
## LEZIONE 6: I CURSORI 
---
(SEZIONE 9)

---
Servono per eseguire Query e Lavorarci in maniera Iterativa
lavorando su un recordSet UNA RIGA ALLA VOLTA

ce ne sono di due tipi:

* ESPLICITI → Definiti dall'utente
* IMPLICITI → Creati automaticamente 

La dichiarazione del cursore equivale alla dichiarazione di un nuovo tipo di dato che contiene i recordset proventienti da una query personalizzata
```sql
DECLARE 
CURSOR c_name IS
    SELECT *
    FROM tabella 
--conterrà i risultati della query
```
Altra parte fondamentale è apertura e chiusura del Cursore (altrimenti rischia di occupare troppa memoria)  
IL flusso di esecuzione è così
<center>
OPEN cursore 

↓ 

FETCH --accedere ai dati

↓

CLOSE cursore
</center>

I cursori Impliciti vengono generati automaticamente con le Query DML e i comandi SELECT X INTO.  
Apertura e Chiusura vengono gestiti automaticamente 

---
### CURSORI ESPLICITI DICHIARAZIONI E CICLI
È possibile utilizzare i cursori tramite i cicli per ottere le informazioni dalla tabella selezionata

*Esempio LOOP semplice*

```sql
DECLARE
   CURSOR my_cursor IS
      SELECT employee_id, first_name, last_name
      FROM employees
      WHERE department_id = 100;
   -- Dichiarazione di variabili per memorizzare i risultati del cursore
   v_employee_id employees.employee_id%TYPE; --con anchor
   v_first_name employees.first_name%TYPE;
   v_last_name employees.last_name%TYPE;
BEGIN
   -- Apertura del cursore
   OPEN my_cursor;
   
   -- Recupero e elaborazione dei dati tramite cursore in un loop
   LOOP
      -- Fetch dei dati dal cursore nel blocco LOOP
      FETCH my_cursor INTO v_employee_id, v_first_name, v_last_name;
      
      -- Uscita dal loop quando non ci sono più righe da leggere
      EXIT WHEN my_cursor%NOTFOUND;
      
      -- Elaborazione dei dati recuperati
      DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || ', Name: ' || v_first_name || ' ' || v_last_name);
   END LOOP;
   
   -- Chiusura del cursore
   CLOSE my_cursor;
END;
```
in questo caso possiamo vedere come operare con i cursori tramite i cicli, punti fondamentali sono :

* dichiarazione delle variabili dove il cursore associerà i valori della query
* apertura e fetch (associazione) dei valori 
* caso di uscita del loop con attributo del cursore (spiegato piu avanti)
* chiusura del cursore
* 
*Esempio ciclo While*
```sql
DECLARE 
CURSOR my_cursor IS 
      SELECT colonna FROM tabella WHERE condizione;

v_cursor my_cursor%ROWTYPE --dichiarazione di una variabile del tipo esatto del cursore in modo da avere in una variabile tutti i campi che il cursore puo riempire 

BEGIN 
  OPEN my_cursor;

   -- Ciclo WHILE per iterare sui risultati del cursore
   WHILE TRUE LOOP
      -- Recupero dei dati dal cursore
      FETCH my_cursor INTO v_cursor

      -- Uscita dal ciclo quando non ci sono più righe da leggere
      EXIT WHEN my_cursor%NOTFOUND;

      -- Elaborazione dei dati recuperati
      DBMS_OUTPUT.PUT_LINE(v_cursor.CAMPO, v_cursor.CAMPO2 ETC)
   END LOOP;

   -- Chiusura del cursore
   CLOSE my_cursor;
END;
```
In questo caso la variabile di tipo cursore immagazina all'interno i campi che il cursore associa ad ogni loop

*Esempio ciclo FOR*
```sql
DECLARE
   -- Dichiarazione del cursore
   CURSOR emy_cursor IS
      SELECT *
      FROM tabella
      WHERE condizione;

  v_cur my_cursor%ROWTYPE; --dichiarazione variabile di tipo cursore;
BEGIN
   -- Ciclo FOR per iterare sui risultati del cursore
   FOR v_cursor IN my_cursor LOOP
      -- Elaborazione dei dati recuperati direttamente dall'iterazione del cursore
      DBMS_OUTPUT.PUT_LINE(v_cursor.Campo);
   END LOOP;

   -- Il cursore viene automaticamente chiuso alla fine del ciclo FOR
END;
```
Unico caso in cuinon possiamo far riferimento alle righe ciclate tramite il `%ROWCOUNT`


Esiste anche l'utilizzo del CURSOR FOR UPDATE:  
Consente di recuperare e bloccare le righe di una tabella all'interno di un cursore in modo che possano essere modificate. Questo tipo di cursore è utile quando si desidera leggere e successivamente aggiornare o eliminare le righe selezionate senza il rischio di conflitti di concorrenza con altre sessioni.   

Infatti le righe influenzate saranno sottoposte ad un **LOCK IN** → altri utenti del DB non potranno accerderci finche la procedura non sarà finita in modo da garantire l'integrità dei dati durante l'operazione

È importante ricordare che questo LOCK sarà presente fin quando il cursore è aperto E finchè non ci sarà un comando di COMMIT o ROLLBACK.  
Stare quindi molto attenti poichè può causare problemi di prestazioni 

---
### ATTRIBUTI DEI CURSORI

Ovviamente è importante la gestione degli errori, per questo utilizziamo in entrambi i casi la clausola %NOTFOUND che serve per fare un check se il cursore sta puntando a null ( la tabella è finita);

proprio per casi come questo esistono gli attributi dei cursori :

| Attributo    | Descrizione                              | 
|--------------|------------------------------------------|
| `%NOTFOUND`  | Restituisce `TRUE` se il cursore non ha più righe da leggere. | 
| `%FOUND`     | Restituisce `TRUE` se il cursore ha ancora righe da leggere. | 
| `%ROWCOUNT`  | Restituisce il numero di righe lette finora dal cursore. | 
| `%ISOPEN`    | Restituisce `TRUE` se il cursore è aperto. | 

---
---
