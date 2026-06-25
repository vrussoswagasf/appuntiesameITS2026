# Compendio di Esercizi e Teoria per Reti informatiche Industriali e Sistemi di Sicurezza Industriali
---

## 1. Bit Destuffing HDLC

### Teoria
Nel protocollo HDLC (High-Level Data Link Control), il delimitatore di frame (flag) è la sequenza `01111110` (6 bit a 1 consecutivi). Per evitare che questa sequenza appaia all'interno del campo dati, il trasmettitore inserisce un bit `0` automatico dopo ogni sequenza di 5 bit `1` consecutivi (**Bit Stuffing**).
Il **Bit Destuffing** è l'operazione inversa compiuta dal ricevitore: quando incontra 5 bit `1` consecutivi, esamina il bit successivo:
- Se è `0`, lo **elimina** (era un bit di stuffing).
- Se è `1` ed il successivo è `0` (quindi `11`), si tratta del flag di chiusura.
- Se è `1` ed il successivo è `1`, è una condizione di errore (abort).

### Esercizio Svolto
**Testo:** Si riceve la seguente sequenza di bit in HDLC: `01111101011111001101111101`. Effettuare il bit destuffing.

**Svolgimento:**
1. Analizziamo la stringa da sinistra a destra contando gli `1` consecutivi:
   - `0`
   - `11111` (5 bit a 1) -> Il bit successivo è `0`. Lo **eliminiamo**.
   - Rimane: `1`
   - `0`
   - `11111` (5 bit a 1) -> Il bit successivo è `0`. Lo **eliminiamo**.
   - Rimane: `011`
   - `0`
   - `11111` (5 bit a 1) -> Il bit successivo è `0`. Lo **eliminiamo**.
   - Rimane: `1`

**Sequenza Originaria Decodificata:** `01111110111110110111111`

---

## 2. Efficienza dei Protocolli (Stop-and-Wait)

### Teoria
L'efficienza $\eta$ di un protocollo di trasmissione rappresenta la frazione di tempo in cui il canale è utilizzato per trasmettere dati utili. Nel protocollo **Stop-and-Wait**:
$$\eta = \frac{T_{tx}}{T_{tx} + 2 \cdot T_{prop} + T_{ack}}$$
Dove:
- $T_{tx} = \frac{\text{Dimensione Frame [bit]}}{\text{Bitrate [bps]}}$ (Tempo di trasmissione)
-  $T_{prop} = \frac{\text{Distanza [m]}}{\text{Velocità di propagazione [m/s]}}$ (Tempo di propagazione)
- Spesso $T_{ack}$ viene considerato trascurabile o pari a $T_{tx\_ack}$.

### Esercizio Svolto
**Testo:** Un collegamento in fibra ottica lungo $200	\text{ km}$ opera a $1	\text{ Mbps}$. La velocità di propagazione è $2 \cdot 10^8	\text{ m/s}$. I pacchetti dati sono lunghi $1000	\text{ byte}$. Gli ACK hanno dimensione trascurabile. Calcolare l'efficienza del protocollo Stop-and-Wait.

**Svolgimento:** 
1. Convertiamo i byte in bit: $\text{Dimensione} = 1000 \times 8 = 8000\text{ bit}$. 
2. Calcoliamo $T_{tx} = \frac{8000\text{ bit}}{10^6\text{ bps}} = 8\text{ ms}$. 
3. Calcoliamo $T_{prop} = \frac{200000\text{ m}}{2 \cdot 10^8\text{ m/s}} = 1\text{ ms}$. 
4. Calcoliamo l'efficienza: $$\eta = \frac{8\text{ ms}}{8\text{ ms} + 2 \cdot 1\text{ ms}} = \frac{8}{10} = 0.8 \quad (80\%)$$

---

## 3. Bit Stuffing Generico

### Teoria
A differenza dell'HDLC, un algoritmo di bit stuffing generico può basarsi su regole custom. Ad esempio, inserire un bit di polarità opposta ogni $N$ bit identici consecutivi, per garantire che ci siano abbastanza transizioni sul canale per il recupero del clock del ricevitore.

### Esercizio Svolto
**Testo:** Applicare un algoritmo di bit stuffing generico che prevede l'inserimento di uno `0` dopo ogni blocco di 4 bit `1` consecutivi sulla stringa: `1111110111101`.

**Svolgimento:**
1. Scorriamo la stringa:
   - Primi 4 bit `1`: `1111` -> inseriamo uno `0` -> `11110`
   - Riprendiamo dai successivi bit: erano `110...` -> ora abbiamo due `1` e uno `0` -> `110`
   - Incontra poi quattro `1`: `1111` -> inseriamo uno `0` -> `11110`
   - Rimane l'ultimo `1`.
2. **Risultato con Stuffing:** `1111 0 110 1111 0 1` (spazi inseriti solo per chiarezza visiva).

---

## 4. Codifiche in Banda Base (Tecniche di Codifica di Linea)

### Teoria
Le codifiche di linea convertono i bit in segnali elettrici. Le principali sono:
- **NRZ-L (Non-Return-to-Zero Level):** Alto per `1` (o `0`), Basso per `0` (o `1`).
- **Manchester:** Transizione a metà del bit. Da Alto a Basso = `0`, Da Basso ad Alto = `1` (convenzione IEEE 802.3).
- **Manchester Differenziale:** Transizione a metà bit per il clock. La presenza di una transizione all'inizio del periodo del bit rappresenta lo `0`, l'assenza rappresenta l'$1$.

### Esercizio Svolto
**Testo:** Rappresentare graficamente (tramite testo/livelli) la sequenza `1011` usando la codifica **Manchester** (IEEE 802.3: `0` = Alto->Basso, `1` = Basso->Alto).

**Svolgimento:**
- Bit `1` $\rightarrow$ Transizione da Basso (B) ad Alto (A) $\rightarrow$ `_|-`
- Bit `0` $\rightarrow$ Transizione da Alto (A) ad Basso (B) $\rightarrow$ `-|_`
- Bit `1` $\rightarrow$ Transizione da Basso (B) ad Alto (A) $\rightarrow$ `_|-`
- Bit `1` $\rightarrow$ Transizione da Basso (B) ad Alto (A) $\rightarrow$ `_|-`

Rappresentazione testuale dell'onda: `_|- -|_ _|- _|-`

---

## 5. Condizionamento del Segnale

### Teoria
I sensori industriali convertono una grandezza fisica (es. temperatura) in una grandezza elettrica (es. resistenza o tensione). Questa grandezza è spesso debole o rumorosa.
Il **condizionamento del segnale** serve a manipolare questo output per renderlo idoneo all'ingresso di un PLC o convertitore ADC. Fasi tipiche:
1. **Amplificazione:** aumenta l'ampiezza del segnale.
2. **Filtraggio:** elimina il rumore ad alta frequenza.
3. **Linearizzazione:** rende proporzionale il segnale all'ingresso.

*Segnale di Ingresso del sensore:* Grandezza fisica (es. Pressione in bar).
*Segnale di Uscita del sensore/condizionatore:* Segnale standard industriale (es. $4-20	\text{ mA}$ o $0-10	\text{ V}$).

### Esercizio Svolto
**Testo:** Un sensore di temperatura lineare ha un range di ingresso di $0-100^\circ	\text{C}$ e fornisce in uscita un segnale di tensione $0-50	\text{ mV}$. Il circuito di condizionamento deve adattare questa uscita allo standard $0-10	\text{ V}$ di un PLC. Quale deve essere il guadagno dell'amplificatore? Se il PLC legge $6	\text{ V}$, che temperatura misura il sensore?

**Svolgimento:**
1. Calcolo del guadagno ($A_v$):
   $$A_v = \frac{V_{out\_max}}{V_{in\_max}} = \frac{10\text{ V}}{0.05\text{ V}} = 200$$
2. Calcolo della temperatura corrispondente a $6	ext{ V}$:
   Dato che il sistema è lineare, l'uscita $0-10	ext{ V}$ mappa proporzionalmente $0-100^\circ	ext{C}$.
   $$\text{Temperatura} = \frac{6\text{ V}}{10\text{ V}} \times 100^\circ\text{C} = 60^\circ\text{C}$$

---

## 6. Topologie di Rete

### Teoria
Le topologie definiscono la disposizione geometrica dei nodi:
- **Bus:** Tutti i nodi sono connessi a un unico canale condiviso (es. CAN, Profibus). Richiede terminatori. Single point of failure sul cavo.
- **Anello:** Ogni nodo è connesso al successivo. Il segnale rigenerato viaggia in una direzione (es. Token Ring).
- **Stella:** Tutti i nodi convergono su un hub/switch centrale (es. Ethernet industriale).

### Esercizio Svolto
**Testo:** In una rete a Bus con 5 nodi distanti $10	ext{ m}$ l'uno dall'altro in linea retta, si verifica un'interruzione del cavo tra il nodo 2 e il nodo 3. Quali nodi possono ancora comunicare tra loro?

**Svolgimento:**
La topologia a bus spezzata si divide in due segmenti isolati.
- Il segmento 1 contiene i nodi 1 e 2.
- Il segmento 2 contiene i nodi 3, 4 e 5.
I nodi {1,2} comunicano tra loro, e i nodi {3,4,5} comunicano tra loro (assumendo che le impedenze di linea rimangano accettabili senza terminazione corretta, teoricamente formano due sottoreti isolate).

---

## 7. Esercizio RS232

### Teoria
La seriale RS232 trasmette in modo asincrono. Un frame è composto da:
- 1 Start bit (sempre a livello logico `0` / tensione positiva)
- $N$ Data bit (solitamente 7 o 8, trasmessi a partire dal **LSB** - bit meno significativo)
- 1 Bit di parità (opzionale)
- 1 o 2 Stop bit (sempre a livello logico `1` / tensione negativa)

### Esercizio Svolto
**Testo:** Calcolare il tempo totale necessario per trasmettere un file di $10	ext{ Kbyte}$ ($1	\text{ Kbyte} = 1024	\text{ byte}$) su una linea RS232 configurata a $9600	ext{ baud}$, con 8 bit di dati, 1 bit di parità e 1 bit di stop.

**Svolgimento:**
1. Calcoliamo i bit totali per singolo carattere (byte):
   $$\text{Bit per frame} = 1\text{ (start)} + 8\text{ (dati)} + 1\text{ (parità)} + 1\text{ (stop)} = 11\text{ bit/carattere}$$
2. Dimensione totale del file in byte = $10 \times 1024 = 10240\text{ byte}$.
3. Bit totali da trasmettere = $10240 \times 11 = 112640\text{ bit}$.
4. Poiché nei sistemi RS232 il baud rate coincide con il bitrate (1 bit per simbolo):
   $$\text{Tempo} = \frac{112640\text{ bit}}{9600\text{ bps}} \approx 11.73\text{ secondi}$$

---

## 8. Arbitraggio CAN (Metodo Grafico)

### Teoria
Il protocollo CAN usa una configurazione "Wired-AND":
- Livello logico `0` = **Dominante** (vince sul bus).
- Livello logico `1` = **Recessivo** (perde sul bus).
L'arbitraggio è non distruttivo e si basa sull'ID del messaggio (l'ID più basso ha priorità maggiore). I nodi trasmettono l'ID e contemporaneamente leggono il bus. Se leggono un bit dominante (`0`) mentre stavano trasmettendo un recessivo (`1`), si mettono in ascolto.

### Esercizio Svolto
**Testo:** Tre nodi CAN (A, B, C) provano a trasmettere contemporaneamente. I loro identificativi a 4 bit sono:
- Nodo A: `1010`
- Nodo B: `1001`
- Nodo C: `1100`
Mostrare graficamente l'arbitraggio bit per bit e determinare chi vince.

**Svolgimento:**

```
Bit:       1°   2°   3°   4°
Nodo A:    1    0    1    0
Nodo B:    1    0    0    1
Nodo C:    1    1    (Perde l'arbitraggio al 2° bit)
--------------------------------------
Stato Bus: 1    0    0    1
```

- **1° Bit:** Tutti trasmettono `1`. Stato Bus = `1`. Tutti continuano.
- **2° Bit:** A trasmette `0`, B trasmette `0`, C trasmette `1`. Poiché `0` è dominante, lo stato del Bus diventa `0`. Il nodo C rileva `0` invece di `1`, capisce di aver perso e si ferma.
- **3° Bit:** A trasmette `1`, B trasmette `0`. Lo stato del Bus diventa `0` (dominante). Il nodo A rileva `0` invece di `1`, perde l'arbitraggio e si ferma.
- **4° Bit:** Rimane solo il nodo B che trasmette `1`. Stato Bus = `1`.

**Vincitore:** Nodo B (ID `1001` è il valore numerico minore).

---

## 9. Tempo di Arbitraggio CAN

### Teoria
Affinché l'arbitraggio funzioni, il bit deve propagarsi da un estremo all'altro della rete e tornare indietro prima del punto di campionamento. Pertanto il tempo di bit ($T_{bit}$) deve soddisfare la relazione:
$$T_{bit} \ge 2 \cdot T_{prop} + T_{interfaccia}$$

### Esercizio Svolto
**Testo:** Una rete CAN ha un tempo di propagazione lungo il cavo pari a $T_{prop} = 220	ext{ ns}$ e un ritardo complessivo dei ricetrasmettitori nei nodi pari a $T_{interfaccia} = 110	ext{ ns}$. Calcolare il tempo minimo di arbitraggio di un bit.

**Svolgimento:**
$$T_{bit\_min} = 2 \cdot (220	\text{ ns}) + 110	\text{ ns} = 440	\text{ ns} + 110	\text{ ns} = 550	\text{ ns}$$

---

## 10. Massimo Bitrate CAN

### Teoria
Il massimo bitrate è semplicemente l'inverso del tempo minimo di bit calcolato per l'arbitraggio:
$$\text{Bitrate}_{max} = \frac{1}{550 \cdot 10^{-9}\text{ s}} \approx 1818181\text{ bps} \approx 1.82\text{ Mbps}$$

### Esercizio Svolto
**Testo:** Utilizzando il tempo minimo di bit trovato nell'esercizio precedente ($550	ext{ ns}$), calcolare il massimo bitrate teorico della rete.

**Svolgimento:**
$$\text{Bitrate}_{max} = \frac{1}{550 \cdot 10^{-9}\text{ s}} \approx 1818181\text{ bps} \approx 1.82\text{ Mbps}$$

---

## 11. Bit Stuffing CAN

### Teoria
Nel CAN, dopo **5 bit consecutivi dello stesso livello logico**, il controllore inserisce automaticamente un **bit di polarità opposta** (bit di stuffing) nei campi dall'inizio del frame fino al CRC (escluso il codice di delimitazione).

### Esercizio Svolto
**Testo:** Data la stringa di bit dati CAN: `0000011111100`, applicare il bit stuffing.

**Svolgimento:**
- `00000` (5 bit a 0) $\rightarrow$ Inserisco un `1` $\rightarrow$ `00000` **`1`**
- Riprendo il flusso originario: i successivi bit sono `11111100`...
- Contiamo i successivi bit a 1: lo stuffing inserito era `1`, seguito dai sei `1` originari. Attenzione: lo stuffing conta nella sequenza! Quindi abbiamo `1` (stuffing) + `1111` (quattro bit originari) = 5 bit `1`.
- Inserisco un `0` di stuffing $\rightarrow$ `0000011111` **`0`**
- Rimangono i bit originari non ancora considerati: due `1` e due `0` $\rightarrow$ `1100`.
- Unendo tutto controlliamo le sequenze: `00000` **`1`** `1111` **`0`** `1100`. No, verifichiamo con attenzione lo scorrimento passo-passo:
  1. `00000` -> Stuffing: `1`. Stringa finora: `000001`
  2. Seguono sei `1` originari. Il primo blocco incontra: `1` (stuffing) + `1111` (originari) = 5 bit `1`. Inserisco `0`. Stringa: `00000111110`
  3. Rimangono gli ultimi due `1` originari e i due `0` originari: `1100`.
  4. Stringa finale complessiva: `00000` **`1`** `1111` **`0`** `1100`. Nessun blocco supera i 5 uguali.

---

## 12. Token Passing (Profibus)

### Teoria
Profibus impiega un metodo di accesso ibrido: **Token Passing** tra i nodi Master (attivi) e **Master-Slave** per i nodi periferici (passivi). Il Token è un frame speciale che conferisce il diritto di trasmissione. Ciascun Master mantiene una tabella dei nodi attivi per conoscere il successore (NS) e il predecessore (PS).

### Esercizio Svolto
**Testo:** In una rete Profibus ci sono tre Master con indirizzi 2, 5, 8. Se il Master 5 possiede il token e ha finito di trasmettere, a chi lo passerà? Come viene configurato l'anello logico?

**Svolgimento:**
L'anello logico si organizza per indirizzi crescenti.
- Master 2 ha come Next Successor (NS) -> 5
- Master 5 ha come Next Successor (NS) -> 8
- Master 8 ha come Next Successor (NS) -> 2
Quando il Master 5 termina il suo ciclo, passa il Token al nodo **8**.

---

## 13 & 14. Token Ring

### Teoria
In una rete Token Ring (IEEE 802.5), un token circola lungo un anello fisico/logico. Un nodo che deve trasmettere cattura il token, cambia un bit trasformandolo in un delimitatore di inizio frame (Start-of-Frame) e appende i dati. Il frame compie l'intero giro, viene letto dal destinatario e rimosso dal mittente originario, che poi genera un nuovo token.

### Esercizio Svolto
**Testo:** Una rete Token Ring a $4	ext{ Mbps}$ ha un anello con una lunghezza di $2	\text{ km}$ e 20 nodi. La velocità del segnale è $2 \cdot 10^8	\text{ m/s}$. Ogni nodo introduce 1 bit di ritardo per la rigenerazione. Calcolare il ritardo totale dell'anello in bit (Ring Latency).

**Svolgimento:**
1. Ritardo di propagazione del cavo:
   $$T_{prop} = \frac{2000\text{ m}}{2 \cdot 10^8\text{ m/s}} = 10^{-5}\text{ s} = 10\text{ }\mu\text{s}$$
2. Convertiamo il ritardo del cavo in bit equivalenti alla velocità della rete:
   $$\text{Bit}_{\text{cavo}} = 10\text{ }\mu\text{s} \times 4\text{ Mbps} = 40\text{ bit}$$
3. Ritardo introdotto dai nodi: $20 \text{ nodi} \times 1\text{ bit/nodo} = 20\text{ bit}$.
4. Ritardo totale (Ring Latency) = $40	\text{ bit} + 20	\text{ bit} = 60	\text{ bit}$.

---

## 15. Accesso al Mezzo (MAC)

### Teoria
I protocolli di controllo dell'accesso al mezzo (MAC) si dividono in:
- **Deterministici:** TDMA, Token Passing, polling (garantiscono un tempo massimo di accesso).
- **Stocastici/Contesa:** CSMA/CD (Ethernet classico), CSMA/CA (Wi-Fi). Non garantiscono il determinismo.

### Esercizio Svolto
**Testo:** Identificare quale metodo di accesso al mezzo è più idoneo per un'applicazione safety-critical in tempo reale tra CSMA/CD e TDMA, giustificando brevemente la risposta.

**Svolgimento:**
Il metodo idoneo è il **TDMA** (o Token Passing). Motivo: Essendo deterministico, riserva slot temporali fissi a ciascun nodo eliminando la possibilità di collisioni. CSMA/CD, basandosi sulla contesa stocastica e su algoritmi di backoff casuali, non può garantire un tempo massimo di trasmissione certo (Bounded Latency), incompatibile con i requisiti di sicurezza.

---

## 16. Scheduling (Rate Monotonic)

### Teoria
Nello scheduling in tempo reale, l'algoritmo **Rate Monotonic (RM)** assegna le priorità in modo statico in base al periodo: minore è il periodo $T_i$, maggiore è la priorità. La condizione sufficiente di ammissibilità (Liu e Layland) per $n$ task è che l'utilizzo della CPU $U$ sia:
$$U = \sum_{i=1}^n \frac{C_i}{T_i} \le n(2^{1/n} - 1)$$
Dove $C_i$ è il tempo di calcolo (computation time).

### Esercizio Svolto
**Testo:** Schedulare con RM due task periodici:
- Task 1: $C_1 = 2$, $T_1 = 5$
- Task 2: $C_2 = 3$, $T_2 = 10$
Verificare se il sistema è sicuramente schedulabile.

**Svolgimento:**
1. Calcolo dell'utilizzo della CPU ($U$):
   $$U = \frac{2}{5} + \frac{3}{10} = 0.4 + 0.3 = 0.7$$
2. Calcolo del limite superiore per $n=2$:
   $$	\text{Limite} = 2(2^{1/2} - 1) = 2(1.414 - 1) = 0.828$$
3. Poiché $U = 0.7 \le 0.828$, il sistema è **sicuramente schedulabile** secondo il criterio di Rate Monotonic.

---

## 17. Calcolo dei Tempi di Propagazione, Trasmissione e Collisione

### Teoria
- $$T_{prop} = \frac{\text{Distanza}}{\text{Velocità}}$$
- $$T_{tx} = \frac{\text{Dimensione}}{\text{Bitrate}}$$
- In una rete a contesa (es. Ethernet), la collisione più tardiva possibile si verifica se un nodo all'estremo opposto inizia a trasmettere un istante prima che arrivi il fronte d'onda del primo nodo. Il tempo massimo per rilevare una collisione è pari a $2 \cdot T_{prop}$ (detto Finestra di Collisione o Slot Time).

### Esercizio Svolto
**Testo:** Due nodi A e B si trovano agli estremi di un cavo di $1	ext{ km}$ ($V_{prop} = 200\text{ m/}\mu	\text{s}$). La velocità è $10	\text{ Mbps}$. Il nodo A inizia la trasmissione al tempo $t = 0$. Il nodo B inizia a trasmettere al tempo $t = 4\text{ }\mu\text{s}$. A che istante avviene la collisione e quando il nodo A se ne accorge?

**Svolgimento:**
1. Calcoliamo: $$T_{prop} = \frac{1000\text{ m}}{2 \cdot 10^8\text{ m/s}} = 5\text{ }\mu\text{s}$$
2. A $t = 4	\text{ }\mu	\text{s}$, il segnale di A ha percorso $4/5$ del cammino. Mancano $200	ext{ metri}$ tra i due fronti d'onda.
3. I due fronti si muovono l'uno contro l'altro alla stessa velocità, quindi si scontrano a metà della distanza rimanente ($100	\text{ m}$). Tempo impiegato per percorrere $100	\text{ m}$: $0.5\text{ }\mu	\text{s}$.
4. **Istante di collisione:** $t_{coll} = 4\text{ }\mu\text{s} + 0.5\text{ }\mu\text{s} = 4.5\text{ }\mu\text{s}$.
5. Il segnale di ritorno (la collisione) deve viaggiare dal punto d'impatto fino ad A. La distanza da A è $900\text{ m}$.
6. Tempo impiegato per fare $900\text{ m}$ ad A: $\frac{900}{200} = 4.5\text{ }\mu\text{s}$
7. **Istante in cui A rileva la collisione:** $t = 4.5	\text{ }\mu\text{s}(\text{istante dell'evento}) + 4.5\text{ }\mu\text{s} = 9\text{ }\mu\text{s}$.

---

## 18. Bit di Parità

### Teoria
Aggiunge un singolo bit alla fine di una parola dati:
- **Parità Pari (Even):** il bit di parità è scelto in modo che il numero totale di `1` nel frame sia pari.
- **Parità Dispari (Odd):** il numero totale di `1` deve essere dispari.
Rileva solo errori singoli (o in numero dispari).

### Esercizio Svolto
**Testo:** Calcolare il bit di parità **pari** e **dispari** per la stringa dati `1011001`.

**Svolgimento:**
1. Contiamo gli `1` nella stringa: ce ne sono 4.
2. **Parità Pari:** Il numero di `1` è già pari (4). Quindi il bit di parità sarà **`0`**. (Stringa completa: `10110010`)
3. **Parità Dispari:** Per rendere dispari il conteggio, serve un ulteriore `1`. Quindi il bit di parità sarà **`1`**. (Stringa completa: `10110011`)

---

## 19. Indirizzamento IP

### Teoria
Dato un indirizzo IP e una Subnet Mask, possiamo determinare:
- L'indirizzo di rete (operazione di AND bit a bit tra IP e Mask).
- L'indirizzo di Broadcast (ponendo a `1` tutti i bit della parte host).
- Il range di IP utilizzabili per gli host.

### Esercizio Svolto
**Testo:** Dato l'indirizzo IP `192.168.1.45` con subnet mask `/26` ($255.255.255.192$), trovare l'indirizzo di rete e l'indirizzo di broadcast.

**Svolgimento:**
1. Analizziamo l'ultimo ottetto ($45$ in binario $\rightarrow$ `00101101`).
2. La maschera `/26` significa che i primi 2 bit dell'ultimo ottetto fanno parte della rete, i restanti 6 dell'host. Maschera dell'ultimo ottetto: `11000000` ($192$).
3. **Indirizzo di Rete:** AND bit a bit sull'ultimo ottetto:
   `00101101` AND `11000000` = `00000000` $\rightarrow$ `.0`
   **IP Rete:** `192.168.1.0`
4. **Indirizzo di Broadcast:** Impostiamo a `1` i 6 bit dell'host nell'ultimo ottetto:
   `00000000` $\rightarrow$ `00111111` che in decimale è $63$.
   **IP Broadcast:** `192.168.1.63`

---

## 20. Parità 2D (LRC / VRC)

### Teoria
Dispone i dati in una matrice bidimensionale. Calcola la parità per ogni riga (VRC) e per ogni colonna (LRC). Permette non solo di rilevare errori multipli, ma anche di **correggere un singolo errore** individuando l'incrocio riga/colonna fallita.

### Esercizio Svolto
**Testo:** Data la matrice di dati con parità **pari** (riga e colonna), individuare se c'è un errore e correggerlo:
```
Parola 1: 1 0 1 1 | 0
Parola 2: 1 1 0 0 | 0
Parola 3: 0 0 1 1 | 0
-------------------
LRC:      0 1 1 0 | 0
```

**Svolgimento:**
1. Verifichiamo le righe (orizzontale):
   - R1: $1+0+1+1 = 3$ (dispari) + bit parità $0 = 3$. **ERRORE sulla riga 1**.
   - R2: $1+1+0+0 = 2$ (pari) + bit parità $0 = 2$. OK.
   - R3: $0+0+1+1 = 2$ (pari) + bit parità $0 = 2$. OK.
2. Verifichiamo le colonne (verticale):
   - C1: $1+1+0 = 2$ (pari) $\rightarrow$ LRC è 0. OK.
   - C2: $0+1+0 = 1$ (dispari) $\rightarrow$ LRC è 1. OK ($1+1=2$).
   - C3: $1+0+1 = 2$ (pari) $\rightarrow$ LRC è 1. **ERRORE sulla colonna 3** ($2+1=3$, non è pari).
1. L'incrocio indica l'errore alla **Riga 1, Colonna 3**. Il bit errato è `1`, quindi il valore corretto è **`0`**.

---

## 21. Checksum (Internet Checksum)

### Teoria
Il checksum (usato in IP/TCP) somma i dati a blocchi di 16 bit usando l'aritmetica in complemento a 1 (l'eventuale riporto oltre il 16° bit va risommato al bit meno significativo). Il risultato finale viene negato bit a bit (NOT).

### Esercizio Svolto
**Testo:** Calcolare il checksum a 4 bit (per semplicità didattica) delle seguenti due parole a 4 bit: `1011` e `0110`.

**Svolgimento:**
1. Somma:
   ```
     1011 +
     0110 =
   -------
    10001  (C'è un riporto di 1 oltre i 4 bit)
   ```
2. Sommiamo il riporto (End-around carry):
   `0001 + 1 = 0010`
3. Complemento a 1 (invertiamo i bit):
   `NOT(0010) = 1101`
**Checksum:** `1101`

---

## 22. Calcolo del CRC (Divisione tra Polinomi)

### Teoria
Il Cyclic Redundancy Check usa l'aritmetica modulare a due elementi (operazione XOR, senza riporto). I dati vengono estesi con tanti zeri quanti il grado del polinomio generatore $G(x)$, dopodiché si esegue la divisione binaria. Il resto della divisione costituisce il CRC.

### Esercizio Svolto
**Testo:** Dati i Dati = `101001` e il Polinomi Generatore $G = 1011$ (grado 3), calcolare i bit di CRC da appendere.

**Svolgimento:**
1. Estendiamo i dati aggiungendo 3 zeri (grado di $G$): `101001000`.
2. Eseguiamo la divisione XOR:

```
101001000 | 1011
1011      | ------
----      | 100101 (quoziente, non rileva)
000101    
   1011   
   ----   
   0000000  -> caliamo i restanti bit fino a trovare un 1
     1000   
     1011   
     ----   
     00110  -> caliamo lo 0 finale
      1011  
      ----  
      0101  <- Resto a 3 bit
```

**Bit di CRC:** `101`

---

## 23. Codice di Hamming

### Teoria
I bit di parità vengono inseriti nelle posizioni corrispondenti alle potenze di 2 ($1, 2, 4, 8, \dots$).
Il numero di bit di controllo $p$ necessari per proteggere $m$ bit dati deve soddisfare la relazione:
$$2^p \ge m + p + 1$$
Ogni bit di parità controlla specifiche posizioni i cui indici binari hanno il bit corrispondente impostato a 1.

### Esercizio Svolto
**Testo:** Calcolare il numero di bit di parità necessari per 4 bit di dati ($m=4$) e formare la sequenza di Hamming per i dati `1101` (usando parità pari).

**Svolgimento:**
1. Calcolo di $p$: proviamo $p=3 \implies 2^3 = 8 \ge 4 + 3 + 1 = 8$. Condizione verificata. Servono **3 bit di parità** ($P_1, P_2, P_4$). Total length = 7 bit.
2. Mappatura posizioni:
   - Pos 1: $P_1$
   - Pos 2: $P_2$
   - Pos 3: $D_1$ (`1`)
   - Pos 4: $P_4$
   - Pos 5: $D_2$ (`1`)
   - Pos 6: $D_3$ (`0`)
   - Pos 7: $D_4$ (`1`)
3. Calcolo dei bit di parità (posizioni in base alla scomposizione binaria dell'indice):
   - $P_1$ controlla posizioni dispari (1, 3, 5, 7) $\rightarrow P_1, 1, 1, 1 \implies$ per parità pari $P_1 = 1$.
   - $P_2$ controlla posizioni (2, 3, 6, 7) $\rightarrow P_2, 1, 0, 1 \implies$ per parità pari $P_2 = 0$.
   - $P_4$ controlla posizioni (4, 5, 6, 7) $\rightarrow P_4, 1, 0, 1 \implies$ per parità pari $P_4 = 0$.

**Sequenza Finale:** `1010101` (Posizioni da 1 a 7).

---

## 24. Rappresentazione degli SRDO in CANopen Safety

### Teoria
Nello standard EN 50325-5 (CANopen Safety), la trasmissione sicura avviene tramite **SRDO** (Safety-Relevant Data Object). Un SRDO è composto da **due messaggi CAN (COB-ID) accoppiati**:
1. Il primo messaggio contiene i dati normali (CAN-ID primario).
2. Il secondo messaggio contiene i dati **invertiti bit a bit** (CAN-ID secondario = CAN-ID primario + 1).
Vengono trasmessi a breve distanza temporale (SCT - Safety Cycle Time).

### Esercizio Svolto
**Testo:** Un sensore di sicurezza CANopen deve trasmettere un byte di stato con valore `0xA5` tramite un SRDO avente COB-ID primario `0x101`. Rappresentare i due frame CAN generati sul bus.

**Svolgimento:**
- **Frame 1 (A):**
  - COB-ID = `0x101`
  - Dati = `0xA5` (in binario: `10100101`)
- **Frame 2 (B):**
  - COB-ID = `0x101 + 1` = `0x102`
  - Dati = `NOT(0xA5)` = `0x5A` (in binario: `01011010`)

---

## 25. Spanning Tree Protocol (Metodo Grafico)

### Teoria
STP elimina i loop logici nelle reti Ethernet a maglia creando un albero senza cicli. Passi:
1. Elezione del **Root Bridge** (lo switch con il Bridge ID più basso).
2. Definizione delle **Root Port** (RP) per gli switch non-root (porta con costo minimo verso il Root).
3. Definizione delle **Designated Port** (DP) su ogni segmento (porta che offre il costo minore verso il Root).
4. Tutte le altre porte vengono messe in stato di **Blocking (Non-designated)**.

### Esercizio Svolto
**Testo:** Tre Switch (S1, S2, S3) formano un triangolo. I loro ID sono: S1 (ID=10), S2 (ID=20), S3 (ID=30). Tutti i collegamenti hanno lo stesso costo pari a 10. Risolvere la topologia indicando i ruoli delle porte.

**Svolgimento:**
1. **S1** ha l'ID più basso (10) $\rightarrow$ è eletto **Root Bridge**. Tutte le sue porte sono **Designated Ports (DP)**.
2. Analisi di **S2**:
   - Porta verso S1: costo = 10.
   - Porta verso S3: costo per arrivare a S1 passandoci = $10 + 10 = 20$.
   - La porta diretta verso S1 è **Root Port (RP)**.
3. Analisi di **S3**:
   - Porta verso S1: costo = 10 $
ightarrow$ **Root Port (RP)**.
4. Analisi del segmento tra S2 e S3:
   - S2 raggiunge il root con costo 10, S3 raggiunge il root con costo 10 (pareggio). Si guarda l'ID dello switch: S2 ha ID minore (20 < 30).
   - La porta di S2 sul segmento S2-S3 diventa **Designated Port (DP)**.
   - La porta di S3 sul segmento S2-S3 viene messa in stato di **Blocking (Altered/Blocked)**.

Il loop è eliminato.

---

## 26. Tempo di Reazione del Sistema di Sicurezza

### Teoria
Il tempo di reazione totale ($T_{total}$) di una catena di sicurezza (Safety Function) è dato dalla somma dei tempi di risposta peggiori di tutti gli elementi coinvolti nel loop di controllo:
$$T_{total} = T_{sensore} + T_{rete} + T_{plc} + T_{attuatore}$$

### Esercizio Svolto
**Testo:** Una barriera ottica ha un tempo di risposta di $15	ext{ ms}$. Trasmette su una rete con tempo massimo di ciclo pari a $10	ext{ ms}$. Il PLC di sicurezza elabora la logica in un tempo massimo di $20	ext{ ms}$. I freni meccanici impiegano $50	ext{ ms}$ per arrestare completamente l'asse. Calcolare il tempo di reazione complessivo del sistema.

**Svolgimento:**
$$T_{total} = 15	ext{ ms} + 10	ext{ ms} + 20	ext{ ms} + 50	ext{ ms} = 95	ext{ ms}$$

---

## 27. Architetture Hardware Ridondate (MooN)

### Teoria
La notazione **MooN** (M out of N) descrive sistemi con $N$ canali paralleli in cui ne servono almeno $M$ funzionanti affinché la funzione di sicurezza venga eseguita (o il sistema rimanga sicuro, a seconda del contesto di safe-state o operatività):
- **1oo1:** Singolo canale. Nessuna tolleranza ai guasti.
- **1oo2:** Due canali in parallelo. Basta che uno solo rilevi il pericolo per attivare la sicurezza (alta sicurezza, rischio di falsi positivi).
- **2oo3:** Tre canali con logica a maggioranza (voto). Combina alta disponibilità e alta sicurezza.

### Esercizio Svolto
**Testo:** Tre sensori di pressione identici sono configurati in un'architettura **2oo3** per attivare una valvola di scarico se la pressione supera i 5 bar. Se i sensori leggono rispettivamente: S1 = 5.2 bar, S2 = 4.8 bar, S3 = 5.4 bar, descrivere l'azione del sistema.

**Svolgimento:**
1. Valutiamo lo stato di ciascun canale rispetto alla soglia di sicurezza (5 bar):
   - S1: $5.2 > 5 \implies$ Attivazione richiesta (Voto = 1).
   - S2: $4.8 \le 5 \implies$ Nessun pericolo rilevato (Voto = 0).
   - S3: $5.4 > 5 \implies$ Attivazione richiesta (Voto = 1).
2. Applichiamo la logica di voto 2 su 3: ci sono due voti favorevoli all'attivazione (S1 e S3).
3. **Risultato:** Poiché $2 \ge M$ ($2 \ge 2$), la logica a maggioranza approva la richiesta e la valvola di scarico viene **attivata**.