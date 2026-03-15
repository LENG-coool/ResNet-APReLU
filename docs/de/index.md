# ResNet-APReLU: Dynamische Anpassung von Aktivierungsfunktionen an Vibrationssignale

## Vorwort

Im Bereich der intelligenten industriellen Instandhaltung ist die Fehlerdiagnose auf Basis von Vibrationssignalen mittels Deep Learning zu einem Mainstream-Ansatz geworden. In der Praxis treten jedoch typischerweise zwei Herausforderungen auf:

- Beim gleichen Fehlertyp unterscheiden sich die Signalmerkmale unter verschiedenen Drehzahlen oder Lasten deutlich (hohe Intra-Klassen-Varianz).
- Bei unterschiedlichen Fehlertypen können die Signale unter bestimmten Betriebsbedingungen stark ähnlich sein (starke Inter-Klassen-Ähnlichkeit).

Klassische CNNs oder ResNets verwenden in der Regel feste Aktivierungsfunktionen (z. B. ReLU oder Leaky ReLU) und führen für alle Eingaben dieselbe nichtlineare Transformation aus. Unter komplexen Betriebsbedingungen ist es mit dieser festen Strategie oft schwierig, Robustheit und Diskriminierbarkeit zugleich zu gewährleisten.

Dieser Beitrag stellt eine in *IEEE Transactions on Industrial Electronics (TIE)* veröffentlichte Arbeit vor: *Deep Residual Networks With Adaptively Parametric Rectifier Linear Units for Fault Diagnosis*. Die Studie schlägt die adaptive Aktivierungsfunktion APReLU vor und verleiht Residualnetzwerken damit eine dynamische nichtlineare Modellierungsfähigkeit, die sowohl auf einzelne Stichproben als auch auf Betriebsbedingungen ausgerichtet ist.

<p align="center">
	<img src="/fig1.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Vergleich von Vibrationssignal-Wellenformen bei unterschiedlichen Drehzahlen und Lasten</p>

Traditionelle Methoden verwenden für alle Signale dieselbe nichtlineare Abbildung. Dadurch lassen sich gleichartige Stichproben aus mehreren Betriebsbedingungen nur schwer wirksam bündeln, während sich unterschiedliche Klassen ebenfalls nur unzureichend trennen lassen. Das begrenzt unmittelbar die Diskriminationsfähigkeit des Merkmalslernens.

## 1. Adaptiv parametrisierte lineare Einheit (APReLU)

Zur Lösung der genannten Probleme schlagen die Autoren APReLU (Adaptively Parametric Rectifier Linear Units) vor.

### 1.1 Kerngedanke

APReLU basiert nicht mehr auf einer festen Aktivierungsformel. Stattdessen erzeugt ein integriertes Subnetzwerk für die aktuelle Eingabe dynamisch die negative Steigung, sodass sich die Aktivierungsfunktion stichprobenabhängig verändert.

<p align="center">
	<img src="/fig3(a).png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Interne Struktur der APReLU-Aktivierungsfunktion</p>

### 1.2 Arbeitsablauf

- **Merkmalsaggregation**: Auf die Eingabe-Feature-Map wird Global Average Pooling (GAP) angewendet, um globale statistische Informationen zu extrahieren.
- **Nichtlineare Transformation**: Über zwei vollverbundene Schichten (FC) und eine BN-Schicht wird die Abbildungsbeziehung "Merkmal-Steigung" gelernt.
- **Dynamische Steigungsgenerierung**: Über Sigmoid oder eine spezifische Abbildung wird eine stichprobenadaptive negative Steigung ausgegeben.

## 2. ResNet-APReLU-Architektur

Die Autoren integrieren APReLU in ein tiefes Residualnetzwerk (ResNet) und bilden so ResNet-APReLU:

- **Rolle von ResNet**: Entschärft Gradientenprobleme beim Training tiefer Netze und unterstützt eine tiefergehende Merkmalsextraktion.
- **Rolle von APReLU**: Führt in jedem Residualblock eine adaptive Aktivierungsfähigkeit ein, sodass das Netzwerk gleichzeitig den "Fehlertyp" und die "an die Betriebsbedingungen gekoppelte optimale nichtlineare Transformation" lernen kann.

<p align="center">
	<img src="/fig3(c).png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Vollständige Netzwerkarchitektur von ResNet-APReLU</p>

## 3. Experimentelle Validierung

Die Untersuchung wurde auf einem Datensatz eines Planetengetriebes durchgeführt und umfasst:

- 8 Gesundheitszustände
- 3 Drehzahlen
- 3 Laststufen

### 3.1 Genauigkeitsergebnisse

Unter verschiedenen Rauschbedingungen (SNR = 5 dB, 3 dB, 1 dB) zeigt ResNet-APReLU gegenüber Verfahren wie ReLU und PReLU bessere Ergebnisse

### 3.2 Merkmalsvisualisierung (t-SNE)


<p align="center">
	<img src="/fig5.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Vergleich der t-SNE-Merkmalsvisualisierung unter einer ConvNet-Architektur mit unterschiedlichen Aktivierungsfunktionen</p>

<p align="center">
	<img src="/fig6.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">t-SNE-Visualisierung der von ResNet-APReLU extrahierten Merkmale</p>

Aus den t-SNE-Ergebnissen ist ersichtlich, dass die von ResNet-APReLU gelernten Merkmale kompaktere Cluster bilden, klarere Klassenränder aufweisen und zwischen unterschiedlichen Fehlerzuständen nahezu keine Überlappung zeigen.

## Informationen zur Publikation

- **Titel**: Deep Residual Networks With Adaptively Parametric Rectifier Linear Units for Fault Diagnosis
- **Zeitschrift**: IEEE Transactions on Industrial Electronics (TIE)
- **DOI**: 10.1109/TIE.2020.2972458
- **Link**: [https://ieeexplore.ieee.org/document/8998530](https://ieeexplore.ieee.org/document/8998530)
