# ResNet-APReLU: rendere le funzioni di attivazione dinamicamente adattive ai segnali di vibrazione

## Prefazione

Nel campo della manutenzione intelligente industriale, la diagnosi dei guasti mediante l'elaborazione dei segnali di vibrazione con il deep learning è diventata un approccio dominante. Nelle applicazioni reali, tuttavia, emergono comunemente due sfide:

- Per uno stesso tipo di guasto, le caratteristiche del segnale possono variare in modo significativo a diverse velocità di rotazione o condizioni di carico (elevata variabilità intra-classe).
- Per tipi di guasto differenti, i segnali possono risultare altamente simili in alcune condizioni operative (forte similarità inter-classe).

Le CNN tradizionali o le ResNet impiegano in genere funzioni di attivazione fisse (come ReLU e Leaky ReLU), applicando la stessa trasformazione non lineare a tutti gli input. In presenza di condizioni operative complesse, questa strategia fissa fatica spesso a bilanciare robustezza e capacità discriminativa.

Questo articolo presenta un lavoro pubblicato su *IEEE Transactions on Industrial Electronics (TIE)*: *Deep Residual Networks With Adaptively Parametric Rectifier Linear Units for Fault Diagnosis*. Lo studio propone APReLU, una funzione di attivazione adattiva che fornisce alle reti residue una capacità di modellazione non lineare dinamica orientata sia al campione sia alla condizione operativa.

<p align="center">
	<img src="/fig1.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Confronto delle forme d'onda dei segnali di vibrazione a diverse velocità di rotazione e carichi</p>

I metodi tradizionali utilizzano una mappatura non lineare uniforme per tutti i segnali, rendendo difficile aggregare efficacemente i campioni della stessa classe in condizioni operative multiple e separare in modo sufficiente i campioni di classi differenti. Questo limita direttamente la capacità discriminativa dell'apprendimento delle caratteristiche.

## 1. Unità lineare rettificata parametrica adattiva (APReLU)

Per affrontare i problemi sopra descritti, gli autori propongono APReLU (Adaptively Parametric Rectifier Linear Units).

### 1.1 Idea centrale

APReLU non dipende più da una formula di attivazione fissa; al contrario, genera dinamicamente la pendenza negativa per l'input corrente attraverso una sottorete interna, realizzando una funzione di attivazione che varia in base al campione.

<p align="center">
	<img src="/fig3(a).png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Struttura interna della funzione di attivazione APReLU</p>

### 1.2 Flusso di lavoro

- **Aggregazione delle caratteristiche**: applicare il global average pooling (GAP) alla mappa delle caratteristiche in ingresso per estrarre informazioni statistiche globali.
- **Trasformazione non lineare**: apprendere la relazione di mappatura "caratteristica-pendenza" tramite due livelli fully connected (FC) e un livello BN.
- **Generazione dinamica della pendenza**: ottenere una pendenza negativa adattiva al campione tramite Sigmoid o una specifica funzione di mappatura.

## 2. Architettura algoritmica di ResNet-APReLU

Gli autori integrano APReLU in una rete residua profonda (ResNet), formando ResNet-APReLU:

- **Ruolo di ResNet**: alleviare i problemi di gradiente nell'addestramento delle reti profonde e supportare l'estrazione di caratteristiche più profonde.
- **Ruolo di APReLU**: introdurre una capacità di attivazione adattiva in ogni blocco residuo, consentendo alla rete di apprendere simultaneamente il "tipo di guasto" e la "trasformazione non lineare ottimale correlata alle condizioni operative".

<p align="center">
	<img src="/fig3(c).png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Architettura completa della rete ResNet-APReLU</p>

## 3. Validazione sperimentale

Lo studio è stato condotto su un dataset di riduttore epicicloidale, che copre:

- 8 stati di salute
- 3 velocità di rotazione
- 3 livelli di carico

### 3.1 Risultati di accuratezza

In diverse condizioni di rumore (SNR = 5 dB, 3 dB, 1 dB), ResNet-APReLU mostra prestazioni superiori rispetto a metodi come ReLU e PReLU

### 3.2 Visualizzazione delle caratteristiche (t-SNE)


<p align="center">
	<img src="/fig5.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Confronto della visualizzazione t-SNE delle caratteristiche in architettura ConvNet con diverse funzioni di attivazione</p>

<p align="center">
	<img src="/fig6.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Visualizzazione t-SNE delle caratteristiche estratte da ResNet-APReLU</p>

Dalla visualizzazione t-SNE si osserva che le caratteristiche apprese da ResNet-APReLU formano cluster più compatti, con confini di classe più chiari e quasi nessuna sovrapposizione tra diversi stati di guasto.

## Informazioni sull'articolo

- **Titolo**: Deep Residual Networks With Adaptively Parametric Rectifier Linear Units for Fault Diagnosis
- **Rivista**: IEEE Transactions on Industrial Electronics (TIE)
- **DOI**: 10.1109/TIE.2020.2972458
- **Link**: [https://ieeexplore.ieee.org/document/8998530](https://ieeexplore.ieee.org/document/8998530)
