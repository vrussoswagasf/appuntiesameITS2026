https://www.youtube.com/playlist?list=PL9_01HM23dGGRUJBNuuEhWjPh3NaGgAE8 (CORSO PLC)

SOMMARIO LEZIONI

[[#EP2 (INPUT OUTPUT)]]
[[#EP3(AUTORITENUTA)]]
[[#EPISODIO 4(SET RESET)]]
[[#EPISODIO 6(TIMER)]]
[[#EP7 (ANALOGICI)]]
[[#EP8 (FRONTE SALITA DISCESA)]]
[[#EP9 BLINK MERKER 1S]]
[[#EP10 BOBINE FRONTE SALITA E DISCESA]]
[[#EP11 CONTATORI CTU]]
[[#EP12 COMANDO AVVIO/ARRESTO]]
[[#EP14 GRAFCET(SFC)]]
[[#EP15 GRAFCET>LADDER]]
# EP2 (INPUT OUTPUT)
tengo premuto input=on 
![[Pasted image 20260617123951.png]]


# EP3(AUTORITENUTA)
due bottoni on/off.  bt1 accende , mantenendo collegato il filo di bt2, però quando clicco bt2 il filo si spezza e cambia valore il led spegnendolo
![[Pasted image 20260617124027.png]]

# EPISODIO 4(SET RESET)
btn1 acceso setta led a verde e resetta rosso, altrimenti se pigio btn2 si setta a rosso e resetta verde
![[Pasted image 20260617124207.png]]

# EPISODIO 6(TIMER):

4 timer:

TP=esegue azione per tot secondi. I SECONDI COMINCIANO DAL MOMENTO IN CUI ATTIVO L'INPUT, ANCHE SE TENGO PREMUTO IL BOTTONE, ARRIVATI I SECONDI, FINISCE L'OUTPUT

TON= esegue output solo se l'azione input viene aggevolata per tot secondi 

TOF= STESSA COSA DEL TP, MA SE TENGO PREMUTO INPUT RIMANE ACCESO E IL MOMENTO IN CUI STACCO L'INPUT, PARTONO I SECONDI E POI SI STACCA L'OUTPUT

TONR= quasi come TON, il valore di tempo di input deve arrivare a tot secondi per accendere output. se lasciamo input prima del limite, salva lo stesso l'istante di secondo e riparte da li se riaggevolo l'input. arrivato al massimo si accende output e si può integrare un reset.

# EP7 (ANALOGICI)
0-10V

NORM_X (INT TO REAL) - SCALE_X(REAL TO INT)
variabile da attivare
ANALOG INT IW64 
ANALOG REAL MD66

![[Pasted image 20260617122944.png|697]]

![[Pasted image 20260617123049.png]]

# EP8 (FRONTE SALITA DISCESA):
![[Pasted image 20260617124731.png]]

emette pulso input a seconda del fronte P=istante iniziale(pigio bottone) N = istante finale (lascio bottone)

# EP9 BLINK MERKER 1S

![[Pasted image 20260618142231.png]]
FIRST SCAN>1S>STATO(LED ACCESO 1S) > FRONTE DISCESA DI STATO > 1S > STATO2(LED SPENTO 1 S) > TORNO SU FRONTE DISCESA STATO2 E RIPARTE IL LOOP
![[Pasted image 20260618142248.png]]

# EP10 BOBINE FRONTE SALITA E DISCESA: 
non troppo diverse da [[#EP8 (FRONTE SALITA DISCESA)]]  però qua sono usate come "output" hanno bisogno di una variabile merker bool per riconoscere se l'impulso è positivo o negativo
![[Pasted image 20260618143716.png]]

# EP11 CONTATORI CTU  

![[Pasted image 20260618145256.png]]

nel blocco CTU: CU dove passa l'impulso, Q dove esce, R per resettare, PV valore da raggiungere per uscire dal contatore, CV per far uscire il valore corrente del contatore del ciclo

su ladder la variabile di conteggio è di tipo INT perciò il suo indirizzo è %MWx

![[Pasted image 20260618145634.png]]

per accendere il led devo cliccare il pulsante dieci volte, al decimo click il led si accende, al prossimo(undicesimo click) il contatore si resetta pertanto il led si spegne.

# EP12 COMANDO AVVIO/ARRESTO

![[Pasted image 20260618154445.png]]

![[Pasted image 20260618154558.png]]
![[Pasted image 20260618154613.png]]

(VERSIONE UTILIZZANDO IL CTU CONTATORE):
![[Pasted image 20260618154759.png]]

# EP14 GRAFCET(SFC)

Sequential Functional Chart

![[Pasted image 20260618155831.png]]
Stati= blocchi di istruzioni da eseguire, passo dopo passo
Transizioni= Condizioni da verificare per passare da uno stato all'altro
Token= gettone che serve per capire in quale stato ci troviamo
N specifica di istanziare l'azione effettiva del blocco

# EP15 GRAFCET>LADDER 

![[Pasted image 20260619112415.png]]
BT1 BT2 input LED1 LED2 output, voglio un programma che quando clicco bt1 si accende led1 e quando clicco bt2 si accende led2.

SFC:
![[Pasted image 20260619112543.png]]
Per tradurre in ladder devo creare dei blocchi.

Predisposizioni è il blocco per istanziare le prime cose per cominciare il ciclo
![[Pasted image 20260619112949.png]]

Azioni è il blocco dove istanziare l'azione da eseguire in output collocando lo stato da cui parte
![[Pasted image 20260619112923.png]]

Ciclo automatico è il centro per fare le transizioni, quando ci troviamo in S0 ed effettuo la condizione(accendo bt1) allora resetto lo stato 0 e setto lo stato1 e quindi dentro azioni vado su S1 che mi accende LED1. tutti i blocchi comunicano allo stesso tempo
![[Pasted image 20260619112905.png]]

su tia portal:
BLOCCO PREDISPOSIZIONI(MAIN)
![[Pasted image 20260619113812.png]]

BLOCCO AZIONI
![[Pasted image 20260619113726.png]]

BLOCCO CICLO AUTOMATICO(TRANSIZIONI)
![[Pasted image 20260619113923.png]]
![[Pasted image 20260619113951.png]]

# EP18 TIMER SU SFC
programma pigio bottone per un secondo e si accende led
introduce il blocco T per il timer, la transizione è quando la variabile di tempo arriva a 1s e parte un nuovo stato

SFC:
![[Pasted image 20260619121148.png]]

AZIONI
![[Pasted image 20260619121422.png]]

TRANSIZIONI:
![[Pasted image 20260619121350.png]]

