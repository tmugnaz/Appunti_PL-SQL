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
## LEZIONE 7 : COLLEZIONI
---
(SEZIONE10)

---
### TIPI DI COLLEZIONI 

Le Collezioni o COLLECTION in inglese sono degli insiemi che raggrupano diversi tipi di dati come tabelle, array etc e sono molto utili per elaborare grandi quantità di dati

Ne esistono diversi tipi:
* TABELLE INDICIZZATE
* ARRAY ASSOCIATIVI
* EMBEDED TABLE
* VARRAY
* RECORD
 
#### TABELLE INDICIZZATE

Sono tabelle riempite solitamente da un cursore a cui per ogni riga viene associato un index che ci permette di scorrerle in maniera efficiente sono quindi **COLLEZIONI ORDINATE DI ELEMENTI** e si creano tramite la sintassi:

```sql
TYPE nomeTabella IS TABLE OF VARCHAR(100) INDEX BY PLS_INTEGER;
--dichiarazione tabella
V_tabella nomeTabella;
-- variabile di tipo nomeTabella
V_index PLS_INTEGER := 0;
--variabile index

CURSOR my_cursor IS
    ( SELECT NOME
    FROM CLIENTI; )
-- creo un cursore per prender ei nomi dei vari clienti
v_cursor my_cursor%ROWTYPE ;
-- variabile di tipo cursore

BEGIN
 OPEN my_cursor
 FOR my_cursor in v_cursor
 LOOP
 v_index := v_index+1; --aumento valore cursore
 v_tabella(v_index):= v_cursor.NOME --associo al posto in posizione index il valore della variabile cursore 
 END LOOP;

WHILE v_index IS NOT NULL --ciclo sugli indici della tabella per scorrerla 
LOOP
 dbms_output.put_line( 'v_tabella.nome'); --stampo i valori in quella posizione 
END LOOP;
EXCETPION WHEN --gestione errori
END;
```
In questo esempio abbiamo ussato un cursore e una variabile indice per riempire ed associare ad ogni riga riempita un valore per poi elaborare i dati tramite un semplice ciclo while.

Le tabelle associative ci permettono di lavorare anche con metodi che si basano sul loro indice:
* .first → indica la prima riga 
  
  `v_tabella(v_tabella.first)`
* .last → indica l'ultima riga
  
  `v_tabella(v_tabella.first)`
* .count → conta il totale delle righe
  
  `if v_tabella.count < X `
* .next→ per passare al valore successivo dell'indice 
---
#### ARRAY ASSOCIATIVO
È molto simile alla Tabella ma invece che un INDEX avremo una chiave che può essere di qualsiasi tipologia di dato
```sql
DECLARE 
  TYPE nomeArray is TABLE OF VARCHAR(100) INDEX BY NUMBER 
  v_array nomeArray;
  v_codice NUMBER

  --creo cursore 
  CURSOR my_cursor  IS 
      SELECT NOME, CODICE FROM
      CLIENTI;

 v_cursor my_cursor%ROWTYPE;

 BEGIN
 FOR v_cursor in my_cursor
 LOOP
 v_array(r_cursor.CODICE):= r_cursor.NOME
 END LOOP;
 --Associo ad ogni valore in posizione codice il corrispondente nome
 v_codice := v_array.first;
 -- CODICE diventa quindi CHIAVE del NOME corrispondente
WHILE v_codice IS NOT NULL
LOOP
dbms_output.put_line( 'il nome :' || v_array(v_codice)|| 'è associato al codice  ' || v_codice)
v_codice := v_array.next(v_codice);
END LOOP;

```
In questo esempio creiamo la variabile codice e associamo il valore di index del privo valore della tabella per poi andare a scorrerlo tramite il .next

---
#### EMBEDDED TABLE
serve a create tabelle dentro tabelle, Collezioni di elementi dello stesso tipo di dati è moltopiu flessibile grazie ai comandi come extend

```sql
DECLARE
    CURSOR c_clienti 
    IS
        SELECT NOME FROM CLIENTI;

--DEFINIZIONE DI UNA NESTED TABLE DI TIPO VARCHAR
TYPE nominativo_type IS TABLE OF VARCHAR2(100);

--DICHIARAZIONE DELLA VARIABILE E INIZIALIZZAZIONE
nominativo_tab nominativo_type := nominativo_type();

v_index PLS_INTEGER := 0;

BEGIN
FOR r_clienti IN c_clienti
    LOOP
        v_index := v_index + 1;
        
        nominativo_tab.EXTEND;
        nominativo_tab(v_index) := r_clienti.Nome;
    END LOOP;
    
    nominativo_tab.EXTEND;
    nominativo_tab(v_index+1) := 'IL MIO NOME';
```
L'inizializzazione della variabile tabella è seguita dal tipo della tabella +(); come sefosse un costruttore.

ci appare quindi chiara la duttilità di questa struttura dati capace di aggiungere record al proprio in terno grazie al comando EXTEND.
Ma non è l'unico comando che possiamo usare, abbiamo anche :
* add → aggiunge l'elemento specificato successivamente in ultima posizione è un extend+insert insieme 
* delete → cancella elemento nella posizione specificata
* count → conta il numero di righe nella tabella
* exist → check se la posizione specificata esiste o meno
* extend(n) → aggiunge n posizioni alla tabella
---
#### VARRAY , Variable size ARRAY

un array con un numero di elementi prestabilito 
```sql
DECLARE 
-- creo cursore 
CURSOR my_cursor IS 
  SELECT NOME FROM CLIENTI;

-- creo VARRAY
 TYPE VARRAY ID VARRAY(X) OF VARCHAR(100);
 v_varray VARRAY(); --stessa inizializzazione della embedded table
 v_index PLS_INTEGER := 0;
 BEGIN
 FOR v_cusror IN my_cursor
 LOOP
 v_index := v_index+1;
 v_varray.extend; --aumento la dimensione
 v_varray(v_index):= v_cursor.nome; --associo alla posizione il contenuto del cursore
 END LOOP
```
Se provo ad inserire un dato in posizione v_varray.lenght+1 mi darà sicurament errore

---
---
## LEZIONE 8 : I RECORD
---
(SEZIONE 11)

---
### TIPI DI RECORD
Servono a combinare dati differenti ma collegati da un'unica unità logica

Ne esistono di 3 tipi:
* Table based 
* cursor based
* Definiti dall'utente
#### TABLE BASED
È un record che contiene diversi CAMPI di tipo uguale alla Tabella di riferimento
```sql
DECLARE 
V_nome TABELLA%ROWTYPE;
--variabile che contiene valori dei tipi dei campi della tabella selezionata
--procedo a riempire il record
SELECT * INTO v_nome
FROM TABELLA
WHERE condizione;
```
È una struttura dati che consente di lavorare con dei dati non caricati da un cursore che neccessita di apertura e chiusura.

---
#### CURSOR BASED
Lo abbiamo incontrato precedentemente è un record formato dai tipi di dato che il cursore andrà a leggere da una query
```sql
CURSOR cursore IS
SELECT* FROM TABLE
v_cursore cursore%ROWTYPE;
FOR v_cursore in cursore 
LOOP
-- riempio il cursore 
```
---
#### DEFINITO DALL'UTENTE
I tipi dei dati che questo cursore puo contenere sono infatti definiti dall'utente
```sql
DECLARE 
TYPE record IS RECORD( NOME VARCHAR(20),
                       CODICE NUMBER);
V_record record%rowtype;
```

È possibile avere anche diversi rcord combinati in quel caso si parla di 
**COLLEZIONI DI RECORD**
```sql
DECLARE 
TYPE miglioriClienti IS RECORD (NOME VARCHAR(20),CODICE NUMBER);

TYPE clientiBest IS TABLE OF miglioriClienti INDEX BY PLS_INTEGER;
v_index NUMBER :=0;
v_clienti clientiBest;
```
---
---
## LEZIONE 9 : STORED PROCEDURE 
---
(SEZIONE 12)

---
Le procedure sono metodi con NOME che vengono salvati sul programma per essere riusati in seguito evitando duplicazione di codice e semplificando le azioni piu comuni.

Si tratta di oggetti del linguaggio PL/SQL

Scopo Principale:

>Le stored procedure sono progettate per eseguire una serie di operazioni specifiche e possono essere utilizzate per eseguire compiti amministrativi, complessi o ripetitivi all'interno di un database.

Ritorno di Valore:
>Le stored procedure possono avere parametri di input e output, ma di solito non restituiscono un valore direttamente. Possono modificare lo stato del database, generare output tramite parametri di output o stampare messaggi di output.

Utilizzo in SQL:  
>Le stored procedure possono essere chiamate da altre procedure, funzioni o applicazioni client, e possono eseguire un insieme di istruzioni SQL complesse.

Gestione Transazionale:
>Le stored procedure possono essere utilizzate per avviare, eseguire e gestire transazioni complesse che coinvolgono più operazioni SQL.
```sql

CREATE OR REPLACE PROCEDURE UpdateEmployeeSalary(
    emp_id IN NUMBER,
    new_salary IN NUMBER
) AS
BEGIN
    UPDATE employees
    SET salary = new_salary
    WHERE employee_id = emp_id;
    COMMIT;  -- Esempio di gestione transazionale
END;
```
I pramentri di `INPUT` devono essere specificati dalla keyword IN seguita dal tipo di valore.  
I pramentri di `OUTPUT` devono essere specificati dalla keyword OUT seguita dal tipo di valore.

Per richiamarla : 
```sql
EXEC UpdateEmployeeSalary(
    emp_id NUMBER,
    new_salary NUMBER)
```
I valori di INPUT possono essere settati a valori di DEFAULT nel caso non siano strettamente necessari.

---
---
## LEZIONE 10 : FUNZIONI
---
(SEZIONE 13)

---
Sono blocchi di codice modulare RIUTILIZZABILI CON OUTPUT

Esistiono sia funzioni di SISTEMA es ROUND(X)
sia quelle Definite dall'utente ovvero metodi con output personalizzati.

Scopo Principale:   
Le funzioni sono progettate per restituire un valore calcolato basato su parametri di input specifici. Possono essere utilizzate per eseguire calcoli, eseguire query e restituire risultati specifici.

Ritorno di Valore:

Le funzioni restituiscono un singolo valore di ritorno tramite la clausola RETURN e possono essere utilizzate in istruzioni SQL per ottenere risultati.

Utilizzo in SQL:

Le funzioni possono essere chiamate direttamente in istruzioni SQL, come parte di una query SELECT, per calcolare valori e restituire risultati.
Limitazioni:

Le funzioni hanno alcune limitazioni rispetto alle stored procedure, ad esempio non possono modificare lo stato del database direttamente tramite DML (Data Manipulation Language) come UPDATE o DELETE senza utilizzare tecniche avanzate.

```sql
CREATE OR REPLACE FUNCTION GetEmployeeName(
    emp_id IN NUMBER
) RETURN VARCHAR2
AS
    v_emp_name VARCHAR2(100);
BEGIN
    SELECT first_name || ' ' || last_name
    INTO v_emp_name
    FROM employees
    WHERE employee_id = emp_id;
    RETURN v_emp_name;
END;
```

Le funzioni sono utilizzate per calcolare e restituire valori specifici e possono essere incorporate direttamente nelle query SQL. La scelta tra stored procedure e funzioni dipende dalle esigenze specifiche dell'applicazione e dal tipo di operazioni che devono essere eseguite.

---
---

## LEZIONE 11 : I TRIGGER
---
(SEZIONE 14)

---
I trigger sono blocchi di codice che andranno ad esegursi in automatico al CHECK di una determinata condizione, e reagiscono a eventi di tipo DML O DDL.

esistono i 
* TRIGGER DI RIGA
* TRIGGER DI TABELLA
* TRIGGER DI ISTRUZIONE
  
E tutti possono distinguersi dal tempismo dell'attivazione : prima o dopo il completamento dell'evento di trigger.

```sql
CREATE OR REPLACE TRIGGER trigger_name
BEFORE / AFTER --tempismo di esecuzione
{DML} ON Tabella --Qualsiasi create delete etc
[FOR EACH ROW] -- specifia opzionale 
BLOCCO DI CODICE --corpo del trigger 
```
Esitono anche delle variabili che possono essere usate con i TRIGGER

**:NEW** e **:OLD**
Sono specifiche per accedere ai date precedenti o successivi ad una data operazione DML.

Normalmente sono associati ad alias che vanno dichiarati con `REFERENCING OLD AS O , NEW AS N`

Esempio:
```sql
CREATE TRIGGER R
BEFORE INSERT ON Tabella T
FOR EACH ROW
BEGIN 
:NEW.CAMPO = Y
:NEW.CAMPO2 = X
END;
```
questo codice ad esempio è un trigger che si attiva quando andiamo ad inserire nella tabella T e serve a settare a dei valori standard dei campi che l'utente non inserisce personalmente (ES. Data creazione etc)

Abbiamo anche la possibilità di utilizzare delle condizioni come ad esempio:
```sql
IF INSERTING THEN --si attiva quando il comandi dml è di inserzione
IF DELETING THEN--si attiva quando il comandi dml è di delezione
IF UPDATING(CAMPO) THEN --si attiva quando il comandi dml è di update su un campo specifico della tabella 
IF UPDATING THEN -- update su tabella ma non specificato il campo → funziona su tutti i campi
WHEN condizione --specifica quando deve attivarsi
```
Uno dei possibili utilizzi è quello di controllo e possiamo andare a impedire l'update di certi campi di una tabella 

Esempio
```sql
CREATE TRIGGER T
BEFORE UPDATE OF CAMPO  C ON TABELLA T
BEGIN 
  RAISE_APPLICATION_ERROR(-20000,'Non è possibile Modificare');
END;
```
Questo trigger andrà a bloccare l'esecuzione di qualsiasi update del campo C 

possiamo anche andare a reagire alle modifiche delle VISTE( QUERY salvate piu semplici e di utilizzo):
```sql
-- Creo una vista
CREATE VIEW my_view AS
SELECT *
FROM table
WHERE scrivania_id = 30;

-- Creazione di un trigger INSTEAD OF per gestire l'inserimento dei dati tramite la vista
CREATE OR REPLACE TRIGGER my_view_insert_trigger
INSTEAD OF INSERT ON my_view
REFERENCING NEW AS N
FOR EACH ROW
BEGIN
    -- Esegui l'operazione di inserimento nella tabella sottostante
    INSERT INTO table (id, nome ,codice,scrivania_id)
    VALUES(N.id, N.name, N.codice, 30);  
END;
```
---
---
## LEZIONE 12 : I PACKAGE 
(SEZIONE 15)

---
Sono degli OGGETTI utilizzati per organizzare e raggruppare le funzioni 

Si dividono in 2 parti:
* SPEC o SPECIFICA 
  >Dichiarazione di funzioni e ivariabili che potranno essere richiamate FUORI dal package è una collezione di Dichiarazioni di Funzioni, Procedure, Metodi e Costanti.
* BODY 
  >Implemetazione delle funzioni e procedure dichiarati nello SPEC, è possibile creare funzioni e metodi che però resteranno a visibilità privata al di fuori del package

Vantaggi dei Package:
- Incapsulamento
- riutilizzo
- Organizzazione logica
- Variabili condivise
- velocità 
---
### SPEC E BODY
Spec
```sql
CREATE OR REPLACE PACKAGE P
AS
-Dichiarazione metodi → nome,tipo,input e output 
-- SOLO LA FIRMA
-Dichiarazione di Variabili
END P;
```
Body
```sql
CREATE OR REPLACE BODY P
AS
  -Corpo dei metodi con la stessa firma nello SPEC 
  CREATE FUNCTION/STOREPROCEDURE
  BEGIN 
  END ;
- Metodi e variabili ausiliari che resteranno invisibili fuori dal package 
END P;
```
Devono essere compilati o contemporaneamente o PRIMA SPEC poi BODY
Per richiamarlo 
`P.nome_Metodo(INPUT)`

Una procedura pubblica può lanciare procedure private che possono a loro volta chiamare variabili private (appunto invisibili se non all'interno del package)

È Presente l'**OVERLOADING** dei metodi all'interno di un PACKAGE basta dare la stessa firma ma diversi input allo stesso metodo.   
nomeMetodo (input1)  
nomeMetodo(input1, input2);

---
---
