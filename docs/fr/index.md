# ResNet-APReLU : rendre les fonctions d'activation dynamiquement adaptatives aux signaux vibratoires

## Préface

Dans le domaine de la maintenance industrielle intelligente, le diagnostic de pannes à partir de signaux vibratoires via l'apprentissage profond est devenu une approche dominante. En pratique, deux défis reviennent fréquemment :

- Pour un même type de panne, les caractéristiques du signal varient fortement selon la vitesse de rotation ou la charge (forte variabilité intra-classe).
- Pour des types de panne différents, les signaux peuvent présenter une forte similarité dans certaines conditions de fonctionnement (forte similarité inter-classes).

Les CNN traditionnels ou les ResNet utilisent généralement des fonctions d'activation fixes (comme ReLU ou Leaky ReLU) et appliquent la même transformation non linéaire à toutes les entrées. Face à des conditions de fonctionnement complexes, cette stratégie fixe peine souvent à concilier robustesse et pouvoir discriminant.

Cet article présente un travail publié dans *IEEE Transactions on Industrial Electronics (TIE)* : *Deep Residual Networks With Adaptively Parametric Rectifier Linear Units for Fault Diagnosis*. Cette étude propose APReLU, une fonction d'activation adaptative qui confère aux réseaux résiduels une capacité de modélisation non linéaire dynamique, orientée à la fois par l'échantillon et par les conditions de fonctionnement.

<p align="center">
	<img src="/fig1.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Comparaison des formes d'onde de signaux vibratoires sous différentes vitesses de rotation et charges</p>

Les méthodes traditionnelles appliquent une même projection non linéaire à tous les signaux, ce qui rend difficile l'agrégation efficace des échantillons d'une même classe dans des conditions multi-régimes, ainsi que la séparation suffisante des classes différentes. Cela limite directement la capacité discriminante de l'apprentissage des caractéristiques.

## 1. Unité linéaire rectifiée paramétrique adaptative (APReLU)

Pour résoudre les problèmes ci-dessus, les auteurs proposent APReLU (Adaptively Parametric Rectifier Linear Units).

### 1.1 Idée centrale

APReLU ne repose plus sur une formule d'activation fixe. À la place, un sous-réseau interne génère dynamiquement la pente négative pour l'entrée courante, de sorte que la fonction d'activation varie selon l'échantillon.

<p align="center">
	<img src="/fig3(a).png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Structure interne de la fonction d'activation APReLU</p>

### 1.2 Processus de fonctionnement

- **Agrégation des caractéristiques** : appliquer un global average pooling (GAP) à la carte de caractéristiques d'entrée afin d'extraire des informations statistiques globales.
- **Transformation non linéaire** : apprendre la relation de mappage « caractéristique-pente » à l'aide de deux couches fully connected (FC) et d'une couche BN.
- **Génération dynamique de pente** : produire une pente négative adaptative à l'échantillon via Sigmoid ou un mappage spécifique.

## 2. Architecture de l'algorithme ResNet-APReLU

Les auteurs intègrent APReLU dans un réseau résiduel profond (ResNet), formant ainsi ResNet-APReLU :

- **Rôle de ResNet** : atténuer les problèmes de gradient lors de l'entraînement des réseaux profonds et permettre l'extraction de caractéristiques plus profondes.
- **Rôle d'APReLU** : introduire une capacité d'activation adaptative dans chaque bloc résiduel, afin que le réseau apprenne simultanément le « type de panne » et la « transformation non linéaire optimale liée aux conditions de fonctionnement ».

<p align="center">
	<img src="/fig3(c).png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Architecture complète du réseau ResNet-APReLU</p>

## 3. Validation expérimentale

L'étude est menée sur un jeu de données de boîte de vitesses planétaire, couvrant :

- 8 états de santé
- 3 vitesses de rotation
- 3 niveaux de charge

### 3.1 Résultats de précision

Sous différentes conditions de bruit (SNR = 5 dB, 3 dB, 1 dB), ResNet-APReLU obtient de meilleures performances que les méthodes ReLU, PReLU, etc. 

### 3.2 Visualisation des caractéristiques (t-SNE)


<p align="center">
	<img src="/fig5.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Comparaison de la visualisation t-SNE des caractéristiques dans une architecture ConvNet avec différentes fonctions d'activation</p>

<p align="center">
	<img src="/fig6.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Visualisation t-SNE des caractéristiques extraites par ResNet-APReLU</p>

Les résultats t-SNE montrent que les caractéristiques apprises par ResNet-APReLU forment des clusters plus compacts, avec des frontières de classes plus nettes et presque aucun chevauchement entre les différents états de panne.

## Informations de l'article

- **Titre** : Deep Residual Networks With Adaptively Parametric Rectifier Linear Units for Fault Diagnosis
- **Revue** : IEEE Transactions on Industrial Electronics (TIE)
- **DOI** : 10.1109/TIE.2020.2972458
- **Lien** : [https://ieeexplore.ieee.org/document/8998530](https://ieeexplore.ieee.org/document/8998530)
