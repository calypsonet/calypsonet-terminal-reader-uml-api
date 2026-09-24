# Changements apportés par la version 3.0.0 des APIs Terminaux

> **Statut du document** : version de travail soumise au **TC Terminal de la CNA pour validation**.

## Préambule

Ce document a pour objectif de **présenter l'intégralité des évolutions des APIs terminaux** entre les **versions Java Keypop actuellement en production** et les **spécifications courantes** des APIs Terminaux CNA — **en vue d'une validation par les membres du TC Terminal** (Technical Committee Terminal) de la **Calypso Networks Association** (CNA).

### Versions comparées

La référence « avant » est constituée des **dernières versions publiées** des modules Java Keypop (tags de production). La référence « après » est constituée des **spécifications normatives** (`index.adoc`) et des diagrammes de classes associés (`uml/class-diagram.puml`) de chaque dépôt `calypsonet-terminal-*-uml-api`.

| API | Référence de spécification | Version Java Keypop en production (avant) | Version spécifiée (après) |
|---|---|---|---|
| Terminal Reader API | CNA-TR-API | `keypop-reader-java-api` **2.1.0** | **3.0.0** |
| Terminal Card API *(interne)* | CNA-TC-API | `keypop-card-java-api` **2.0.1** | **3.0.0** |
| Terminal Calypso Card API | CNA-TCC-API | `keypop-calypso-card-java-api` **2.2.0** | **3.0.0** |
| Terminal Reader Definitions API *(nouvelle)* | CNA-TRD-API | — | **1.0.0** |
| Terminal Calypso Crypto Legacy SAM API | CNA-TCCL-API | `keypop-calypso-crypto-legacysam-java-api` **1.0.0** | **2.0.0** |
| Terminal Calypso Crypto Symmetric API | CNA-TCCS-API | `keypop-calypso-crypto-symmetric-java-api` **0.1.1** | **0.2.0** |
| Terminal Calypso Crypto Asymmetric API | CNA-TCCA-API | `keypop-calypso-crypto-asymmetric-java-api` **0.2.0** | **0.3.0** |
| Terminal Generic Card API | CNA-TGC-API | `keypop-genericcard-jvm-api` **1.0.0** | **2.0.0** |
| Terminal Storage Card API | CNA-TSC-API | `keypop-storagecard-java-api` **1.2.0** | **2.0.0** |

Toutes les spécifications sont actuellement en version `…-SNAPSHOT`.

Il décrit, pour chaque thème d'évolution :

- la **motivation** (le « pourquoi ») ;
- le **détail des changements** dans chacune des APIs concernées ;
- la **justification de conception** (le « pourquoi de ce choix-là plutôt qu'un autre »).

L'**annexe A** fournit en complément une **correspondance exhaustive, API par API**, entre chaque élément des versions Java en production et son devenir dans les spécifications.

L'alignement des implémentations Java Keypop sur ces versions et la rédaction d'un **guide technique de migration** à destination des intégrateurs interviendront ultérieurement, après validation par le TC Terminal (cf. §18).

> **Visibilité des APIs vis-à-vis des audiences**
>
> - La **Reader API**, la **Calypso Card API**, la **Terminal Reader Definitions API**, la **Legacy SAM API**, la **Generic Card API** et la **Storage Card API** sont des APIs **publiques**, manipulées directement par l'**intégrateur** (le code applicatif).
> - La **Card API** est une API **interne** : elle sert de contrat d'intégration entre les implémentations de lecteurs et les extensions de cartes. **L'intégrateur n'y a pas accès**.
> - Les **Crypto Symmetric API** et **Crypto Asymmetric API** définissent les contrats (SPI) entre la Calypso Card API et les modules cryptographiques ; l'intégrateur ne les manipule qu'indirectement, à travers les modules crypto qu'il instancie (par exemple la Legacy SAM API).
>
> Le présent document décrit les évolutions de toutes les APIs impactées car elles sont solidaires sur le plan de la conception. Les évolutions des APIs internes ou de contrat ne nécessitent **aucune action** de la part de l'intégrateur ; elles sont absorbées par les implémentations Keypop.

> **Nouvelle API : Terminal Reader Definitions API**
>
> La version 3.0.0 introduit une nouvelle API **socle** dédiée à l'hébergement des **types énumérés transverses** aux APIs Terminaux. Elle est créée à l'occasion du Thème 6 pour accueillir `RfTechnology` et `CardType`, mais sa vocation est plus large : elle est **destinée à recevoir potentiellement d'autres énumérations** qui constituent des **constantes globales** partagées par plusieurs APIs Terminaux. La Terminal Reader API **dépend** désormais de cette nouvelle API.
>
> Concrètement, cela se traduit par :
>
> - un **nouveau dépôt** : `calypsonet-terminal-reader-definitions-uml-api` (version `1.0.0-SNAPSHOT`) ;
> - un **nouveau module Java Keypop** : `keypop-reader-definitions-jvm-api` (à créer, conformément à la convention de nommage Keypop) ;
> - une **dépendance** déclarée du module `keypop-reader-java-api` vers ce nouveau module.

> **Nouvelle forme des livrables : des spécifications normatives indépendantes du langage**
>
> Jusqu'ici, les APIs Terminaux étaient décrites par un diagramme UML et par la Javadoc des modules Java Keypop. Chaque API dispose désormais d'une **spécification normative** (`index.adoc`), rédigée dans une **notation indépendante du langage d'implémentation** (inspirée de Kotlin), accompagnée de son diagramme de classes. Cette nouvelle proposition de conception a pour objectif d'**élargir le choix des langages d'implémentation** des APIs Terminaux au-delà de Java, par exemple **Kotlin Multiplatform (KMP)**, **Rust**, **Swift** ou **C#**. Ce changement de forme a des conséquences sur la manière dont les types et les opérations sont exprimés ; elles sont décrites au **Thème 10** (§11).

---

## Documents de référence

Chaque dépôt `calypsonet-terminal-*-uml-api` hébergé sur [github.com/calypsonet](https://github.com/calypsonet/) contient, à sa racine :

- la **spécification normative** `index.adoc` ;
- le **diagramme de classes** `uml/class-diagram.puml`, dont le contenu est strictement aligné sur la spécification.

| Module | Dépôt | Version |
|---|---|---|
| **Terminal Reader API** | [calypsonet-terminal-reader-uml-api](https://github.com/calypsonet/calypsonet-terminal-reader-uml-api) | 3.0.0-SNAPSHOT |
| **Terminal Card API** *(interne)* | [calypsonet-terminal-card-uml-api](https://github.com/calypsonet/calypsonet-terminal-card-uml-api) | 3.0.0-SNAPSHOT |
| **Terminal Calypso Card API** | [calypsonet-terminal-calypso-card-uml-api](https://github.com/calypsonet/calypsonet-terminal-calypso-card-uml-api) | 3.0.0-SNAPSHOT |
| **Terminal Reader Definitions API** *(nouveau)* | [calypsonet-terminal-reader-definitions-uml-api](https://github.com/calypsonet/calypsonet-terminal-reader-definitions-uml-api) | 1.0.0-SNAPSHOT |
| **Terminal Calypso Crypto Legacy SAM API** | [calypsonet-terminal-calypso-crypto-legacysam-uml-api](https://github.com/calypsonet/calypsonet-terminal-calypso-crypto-legacysam-uml-api) | 2.0.0-SNAPSHOT |
| **Terminal Calypso Crypto Symmetric API** | [calypsonet-terminal-calypso-crypto-symmetric-uml-api](https://github.com/calypsonet/calypsonet-terminal-calypso-crypto-symmetric-uml-api) | 0.2.0-SNAPSHOT |
| **Terminal Calypso Crypto Asymmetric API** | [calypsonet-terminal-calypso-crypto-asymmetric-uml-api](https://github.com/calypsonet/calypsonet-terminal-calypso-crypto-asymmetric-uml-api) | 0.3.0-SNAPSHOT |
| **Terminal Generic Card API** | [calypsonet-terminal-genericcard-uml-api](https://github.com/calypsonet/calypsonet-terminal-genericcard-uml-api) | 2.0.0-SNAPSHOT |
| **Terminal Storage Card API** | [calypsonet-terminal-storagecard-uml-api](https://github.com/calypsonet/calypsonet-terminal-storagecard-uml-api) | 2.0.0-SNAPSHOT |

> Les anciens diagrammes publiés dans les dossiers `…-SNAPSHOT/` (`api_class_diagram.svg` et `api_class_diagram_diff.svg`) reflètent un **état intermédiaire** des travaux et ne sont plus à jour ; la référence est désormais la spécification `index.adoc`.

**Conventions de lecture des diagrammes** :

- les éléments en **bleu** sont les **ajouts ou modifications** de la nouvelle version ;
- les éléments en **gris** sont **en cours d'étude** (« work in progress ») : ils n'ont jamais été implémentés et ne font pas partie du périmètre normatif (cf. §17) ;
- les classes de couleur verte regroupent les **données, constantes, énumérations et erreurs** ;
- les signatures suivent la notation indépendante du langage décrite au Thème 10 (`→ Self`, `T?`, `val propriété: Type = défaut`, etc.).

---

## Table des matières

1. [Vue d'ensemble](#1-vue-densemble)
2. [Thème 1 — Support des canaux logiques multiples](#2-thème-1--support-des-canaux-logiques-multiples)
3. [Thème 2 — Contre-mesures temporelles : attaque relai et émulation de carte](#3-thème-2--contre-mesures-temporelles--attaque-relai-et-émulation-de-carte)
4. [Thème 3 — Simplification de la gestion de l'observation](#4-thème-3--simplification-de-la-gestion-de-lobservation)
5. [Thème 4 — Connaissance de l'état courant de la session sécurisée](#5-thème-4--connaissance-de-létat-courant-de-la-session-sécurisée)
6. [Thème 5 — Améliorations sémantiques (renommages et suppressions)](#6-thème-5--améliorations-sémantiques-renommages-et-suppressions)
7. [Thème 6 — Typage strict des technologies RF et des types de carte (support ECP)](#7-thème-6--typage-strict-des-technologies-rf-et-des-types-de-carte-support-ecp)
8. [Thème 7 — Identification des commandes (`commandId`)](#8-thème-7--identification-des-commandes-commandid)
9. [Thème 8 — Découverte et accès normalisés aux lecteurs (`CardReaderProvider`)](#9-thème-8--découverte-et-accès-normalisés-aux-lecteurs-cardreaderprovider)
10. [Thème 9 — Refonte du modèle de sélection de cartes](#10-thème-9--refonte-du-modèle-de-sélection-de-cartes)
11. [Thème 10 — Spécification indépendante du langage d'implémentation](#11-thème-10--spécification-indépendante-du-langage-dimplémentation)
12. [Thème 11 — Données exposées sans calcul et accès aux données brutes](#12-thème-11--données-exposées-sans-calcul-et-accès-aux-données-brutes)
13. [Thème 12 — Opérations Stored Value (SV)](#13-thème-12--opérations-stored-value-sv)
14. [Thème 13 — Tolérance du fichier ou de l'enregistrement absent en session sécurisée](#14-thème-13--tolérance-du-fichier-ou-de-lenregistrement-absent-en-session-sécurisée)
15. [Thème 14 — Extensions crypto et entrelacement des commandes](#15-thème-14--extensions-crypto-et-entrelacement-des-commandes)
16. [Clarifications normatives](#16-clarifications-normatives)
17. [Éléments en cours d'étude](#17-éléments-en-cours-détude)
18. [Procédure de migration](#18-procédure-de-migration)
19. [Suite et validation par le TC Terminal](#19-suite-et-validation-par-le-tc-terminal)
- [Annexe A — Correspondance détaillée par API](#annexe-a--correspondance-détaillée-par-api)

---

## 1. Vue d'ensemble

La nouvelle génération des APIs Terminaux introduit des ruptures de compatibilité sur l'ensemble des APIs existantes, **crée une nouvelle API socle** (`Terminal Reader Definitions API`), et s'accompagne d'un **changement de forme** des livrables (spécifications normatives indépendantes du langage). Les changements sont regroupés en quatorze thèmes :

| # | Thème | Reader | Card | Calypso Card | Definitions | Legacy SAM | Crypto Sym. | Crypto Asym. | Generic Card | Storage Card |
|---|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 1 | Canaux logiques multiples | ● | ● | ● | — | — | — | — | ● | — |
| 2 | Contre-mesures relai et émulation | — | ● | ● | — | — | — | — | ● | ● |
| 3 | Simplification de l'observation | ● | — | — | — | — | — | — | — | — |
| 4 | État courant de la session sécurisée | — | — | ● | — | — | — | — | — | — |
| 5 | Améliorations sémantiques | ● | ● | ● | — | ● | ● | ● | ● | ● |
| 6 | Typage RF / types de carte (ECP) | ● | — | — | ● (création) | — | — | — | — | — |
| 7 | Identification des commandes (`commandId`) | — | — | ● | — | ● | — | — | ● | ● |
| 8 | Découverte normalisée des lecteurs | ● | — | — | — | — | — | — | — | — |
| 9 | Refonte du modèle de sélection | ● | — | — | — | — | — | — | — | — |
| 10 | Spécification indépendante du langage | ● | ● | ● | ● | ● | ● | ● | ● | ● |
| 11 | Données sans calcul, données brutes | — | ● | ● | — | ● | ● | ● | — | ● |
| 12 | Opérations Stored Value | — | — | ● | — | — | — | — | — | — |
| 13 | Tolérance du fichier ou enregistrement absent | — | — | ● | — | — | — | — | — | — |
| 14 | Extensions crypto et entrelacement | — | — | ● | — | ● | — | — | — | — |

Les conséquences transverses :

- **Suppression de tout l'héritage déprécié** : tous les éléments marqués `@Deprecated` dans les versions en production sont supprimés sans alternative de compatibilité (par exemple `ChannelControl` dans la Calypso Card API, `TransactionManager.processCommands()` dans la Legacy SAM API, `prepareReadSystemBlock()` dans la Storage Card API).
- **Suppression des éléments « work in progress »** qui figuraient dans les anciens diagrammes à titre prospectif sans avoir été implémentés : `ReaderApiFactory.createMultichannelCardSelector()`, `MultichannelCardSelector`, `CardSelectionResult.getCardReader()` (Reader API) ; `AsymmetricCryptoSecuritySetting.authorizeAllTrustedCa()`, `authorizeOnlyConfiguredCa()` et `revokeCa(byte[])` (Calypso Card API). Les éléments encore à l'étude dans la Legacy SAM API sont listés au §17.
- **Disparition complète de `ChannelControl`** (Reader API, Card API, Calypso Card API) : la fermeture du canal devient une **opération explicite** (`closeChannel`, `transmitCardRequestAndCloseChannel`, `processCommandsAndCloseChannel`).
- **Disparition des doublons d'erreurs** : l'exception `InvalidCardResponseException` du package `reader.selection`, ainsi que les erreurs de communication et de statut propres à la Calypso Card API et à la Legacy SAM API, disparaissent au profit des erreurs de la Reader API.
- **Généricité récursive abandonnée** : toutes les interfaces de la forme `T extends X<T>` (gestionnaires de transactions, sélecteurs, données de signature) perdent leur paramètre de type ; le chaînage fluide est exprimé par le type de retour `Self` (cf. Thème 10).

---

## 2. Thème 1 — Support des canaux logiques multiples

### 2.1 Motivation

Jusqu'ici, l'API supposait implicitement qu'un seul canal logique était ouvert à la fois entre le terminal et la carte. La 3.0.0 introduit explicitement la notion de **canaux logiques multiples** (cartes ISO supportant plusieurs sélections d'application simultanées sur des canaux logiques distincts), avec deux objectifs :

1. permettre de sélectionner et de manipuler **plusieurs applications** d'une même carte en parallèle ;
2. distinguer clairement, pour chaque carte intelligente, **sur quel canal** elle est attachée et **si elle est encore active**.

Le déclencheur concret de ce chantier est l'arrivée d'un nouveau produit CNA, **OpenSAM**, dont le modèle d'usage repose sur la **cohabitation simultanée de plusieurs applications de sécurité** accessibles en parallèle sur des canaux logiques distincts. Les spécifications détaillées d'OpenSAM relèvent de la documentation CNA dédiée à ce produit.

### 2.2 Reader API

#### Sélection multicanal

- **Nouveau gestionnaire** `MultichannelCardSelectionManager`, obtenu par `ReaderApiFactory.createMultichannelCardSelectionManager()`, dont l'opération `processCardSelectionScenario(reader: CardReader, channelSelectionPolicy: ChannelSelectionPolicy) → MultichannelCardSelectionResult` exécute le scénario en plaçant **chaque sélection réussie sur son propre canal logique**. Si la carte présentée ne supporte pas le multicanal, une erreur `InvalidCardResponse` est levée à l'exécution du scénario. Ce gestionnaire s'inscrit dans la refonte générale de la sélection décrite au **Thème 9** (§10).
- **Nouvelle énumération** `ChannelSelectionPolicy` :
  - `ALLOW_BASIC_CHANNEL` — autorise l'usage du canal de base (canal 0) en plus des canaux logiques additionnels ;
  - `LOGICAL_CHANNEL_ONLY` — restreint la sélection aux canaux logiques supplémentaires (le canal 0 n'est pas utilisé).
- **Nouveau résultat** `MultichannelCardSelectionResult` (`cardType`, `smartCards`) : toutes les cartes qu'il expose sont actives en parallèle, chacune sur son canal. Le mode planifié (sélection sur insertion de carte) n'est pas proposé en multicanal.

#### Connaissance du canal au niveau de la carte

- **Nouvelle opération** `SmartCard.isActive() → Boolean` — la carte sait si elle est encore active sur son canal.
- **Nouvelle opération** `IsoSmartCard.isBasicChannel() → Boolean` — indique si la carte est attachée au canal de base ou à un canal logique supplémentaire.

#### Gestion transactionnelle multicanal

La hiérarchie des gestionnaires de transactions (namespace `reader.transaction.spi`) est restructurée en trois niveaux :

```text
CardTransactionManager  (interface racine, non générique)
  ├─ IsoCardTransactionManager  (nouvelle, ISO 7816-4 — porte la conversion vers le multicanal)
  └─ MultichannelCardTransactionManager  (nouvelle, multicanal effectif)
```

- **`CardTransactionManager`** (refondue) — n'est plus générique ; expose `processCommands() → Unit` (sans paramètre). C'est l'interface racine commune à tous les gestionnaires de transactions.
- **`IsoCardTransactionManager`** — **nouvelle interface** intermédiaire dédiée aux cartes ISO 7816-4. Elle expose une unique opération `asMultichannelCardTransactionManager() → MultichannelCardTransactionManager`, qui renvoie une vue multicanal du gestionnaire. **La conversion elle-même n'échoue jamais** : si la carte sous-jacente ne supporte pas le multicanal, l'erreur `InvalidCardResponse` n'est levée qu'**au traitement des commandes** par le gestionnaire obtenu (`processCommands`, `processCommandsAndCloseChannel`).
- **`MultichannelCardTransactionManager`** — **nouvelle interface** étendant `CardTransactionManager` ; expose :
  - `processCommandsAndCloseChannel() → Unit` — exécute les commandes en attente et, en cas de succès, **ferme le canal** ;
  - `closeChannel() → Unit` — fermeture explicite du canal, idempotente.

> **Ancrage attendu côté APIs consommatrices** :
>
> - Les APIs ciblant des cartes ISO 7816-4 **non intrinsèquement multicanal** (Calypso Card, Generic Card) font étendre leur gestionnaire de transactions de **`IsoCardTransactionManager`** et accèdent au multicanal **à la demande** via `asMultichannelCardTransactionManager()`.
> - Les APIs ciblant des cartes **intrinsèquement multicanal** (la **future Terminal OpenSAM API** en particulier) font étendre leur gestionnaire de transactions **directement de `MultichannelCardTransactionManager`**.

### 2.3 Card API

- **Nouvelle opération** `SmartCardSpi.deactivate() → Unit` — permet au lecteur de **désactiver** la carte, de sorte que l'application voie immédiatement `SmartCard.isActive()` passer à `false`.
- **Nouvelle interface SPI** `MultichannelSmartCardSpi` (étend `SmartCardSpi`) avec `getChannel() → Int`.
- **Refonte de `ProxyReaderApi`** :
  - **supprimées** : `transmitCardRequest(CardRequestSpi, ChannelControl)` et `releaseChannel()` ;
  - **ajoutées** :
    - `transmitCardRequest(cardRequest: CardRequest, smartCard: SmartCardSpi) → CardResponse` ;
    - `transmitCardRequestAndCloseChannel(cardRequest: CardRequest, multichannelSmartCard: MultichannelSmartCardSpi) → CardResponse` ;
    - `closeChannel(multichannelSmartCard: MultichannelSmartCardSpi) → Unit`.
- **Ajout** de la propriété `CardSelectionResponse.channel: Int` — la réponse de sélection porte le numéro du canal (`0` en mono-canal).
- **Suppression** de `CardResponseApi.isLogicalChannelOpen()` — devenue redondante avec le nouveau modèle.

#### 2.3.1 Rôle de la carte transmise au `ProxyReaderApi`

Le paramètre `SmartCardSpi` / `MultichannelSmartCardSpi` n'est **pas** un simple véhicule pour le numéro de canal. Il joue jusqu'à trois rôles :

1. **transporter le numéro de canal logique** (uniquement avec `MultichannelSmartCardSpi`) ;
2. **transporter l'état actif de la carte**, afin que le lecteur vérifie qu'elle est encore active avant toute émission — une carte inactive provoque l'erreur `CardBrokenCommunication` ;
3. **permettre au lecteur de désactiver la carte** (`SmartCardSpi.deactivate()`), par exemple après une erreur de communication ou à la fermeture explicite du canal.

#### 2.3.2 Cycle de vie des `SmartCard`

Le lecteur **conserve les références aux `SmartCard` issues de la dernière sélection** et les **désactive** dans quatre cas :

1. lors d'une **nouvelle sélection en mode mono-canal** ;
2. lors d'une **demande explicite de fermeture du canal** (`closeChannel`, `processCommandsAndCloseChannel`) ;
3. lors de l'appel à **`ObservableCardReader.endCardProcessing()`** ;
4. lors d'une **erreur indiquant que la carte n'est plus contactable** (`CardCommunication`, `ReaderCommunication`).

Ce contrat, qui ne figurait qu'en prose dans la version précédente de ce document, est désormais **normatif** : il est défini dans la spécification de la Reader API (description de `SmartCard.isActive`, section *SmartCard lifecycle*).

### 2.4 Calypso Card API

- Le `TransactionManager` Calypso étend désormais **`IsoCardTransactionManager`** (au lieu de `CardTransactionManager`). L'accès au multicanal s'effectue **sans opération dédiée** : l'intégrateur appelle `asMultichannelCardTransactionManager()` puis utilise `processCommandsAndCloseChannel()` / `closeChannel()`.

### 2.5 Generic Card API

- Le gestionnaire de transactions (renommé `GenericCardTransactionManager`, cf. Thème 5) étend désormais **`IsoCardTransactionManager`**.

### 2.6 Justification

Le contrôle « par paramètre » (`ChannelControl.KEEP_OPEN` / `CLOSE_AFTER`) reposait sur une notion implicite et globale d'« unique canal courant ». Dans un contexte multicanal, ce modèle est ambigu : sur quel canal s'applique le `CLOSE_AFTER` ? Le passage à un modèle où la cible (la `SmartCard(Spi)`) est **explicitement nommée** dans chaque appel résout cette ambiguïté.

La hiérarchie à trois niveaux permet à **chaque API consommatrice de s'ancrer au niveau de capacité qui correspond exactement à son modèle de carte** : conversion à la demande pour les cartes dont le multicanal n'est qu'une capacité optionnelle, ancrage direct pour les cartes intrinsèquement multicanal.

---

## 3. Thème 2 — Contre-mesures temporelles : attaque relai et émulation de carte

### 3.1 Motivation

Deux menaces se détectent par la **durée des échanges**, et les nouvelles versions introduisent un mécanisme commun pour les deux.

- L'**attaque relai** consiste à relayer le dialogue avec une carte vers un emplacement distant, ce qui rend possible une opération frauduleuse à l'insu du porteur. Le relai ajoute un délai de transmission : un échange anormalement long peut donc révéler que la carte n'est pas réellement présente devant le lecteur.
- L'**émulation de carte** consiste à faire répondre un matériel RFID générique à la place de la carte attendue. Ce matériel traite la commande par logiciel, là où la puce répond de façon câblée : un échange anormalement long révèle alors que la réponse ne vient pas du produit attendu. Cette menace concerne surtout les **cartes de stockage**, dépourvues de mécanisme cryptographique. Les nouvelles versions introduisent un mécanisme de **mesure et de bornage des durées d'échange APDU** et de **bornage de la durée de session sécurisée**.

#### Modèle de menace retenu

- **Surface d'attaque visée** : **attaque applicative** (relai logiciel des APDU, émulation de carte par un matériel générique), par opposition aux attaques au niveau du transport RF physique qui relèvent de contre-mesures matérielles.
- **Unité** des bornes et des durées mesurées : la **microseconde** (`µs`). La milliseconde est trop grossière pour les échanges les plus courts, notamment la lecture d'une carte de stockage, qui dure environ 2 ms. La **résolution effective de la mesure dépend de l'implémentation**, qui doit la documenter.
- **Lieu de mesure** : l'**implémentation de la Terminal Reader API** mesure la durée effective de chaque échange APDU et la compare à la borne déclarée sur la requête. Les bornes de durée Calypso sont déclarées dans la Calypso Card API et portent chacune sur **le seul échange d'une commande** (*Open Secure Session*, *Close Secure Session*, *SV Reload* / *SV Debit* / *SV Undebit*).
- **Comportement post-dépassement** : la Card API lève l'erreur **`ApduExchangeDurationExceeded`**, que les extensions de plus haut niveau interceptent et propagent à l'application sous forme d'**`InvalidCardResponse`**. La Calypso Card API précise désormais ce comportement : si une **session sécurisée est ouverte, elle est automatiquement annulée** avant la remontée de l'erreur, de sorte qu'aucune modification de la session ne soit validée par la carte ; **hors session** (commande SV), il n'y a rien à annuler et seule l'erreur remonte à la couche billettique, qui décide de la suite selon son contexte. Dans la Generic Card API, un dépassement lève `InvalidCardResponse`, dont le message identifie la commande fautive.

### 3.2 Card API

- **Côté requête** : `ApduRequest.apduExchangeMaxDuration: Long? = null` — durée maximale tolérée pour l'échange (en microsecondes) ; `null` signifie « pas de borne ».
- **Côté réponse** : `ApduResponse.apduExchangeDuration: Long?` — durée effective de l'échange ; `null` signifie « durée non mesurée ».
- **Nouvelle erreur** `ApduExchangeDurationExceeded` — levée par `ProxyReaderApi.transmitCardRequest(...)` lorsque la durée effective dépasse la borne déclarée. Elle porte, comme les autres erreurs APDU, `cardResponse` et `isCardResponseComplete`.
- La spécification de la Card API documente désormais ce mécanisme comme **solution pratique pour mettre en œuvre des contre-mesures anti-relai** (chapitre *APDU exchange execution-time control*).

### 3.3 Calypso Card API

Chaque borne se déclare selon deux familles de réglages, chacune avec une opération dédiée :

- **par CSN** (`…ByCsn(maxDuration: Long, csnMin: Long)`) : `csnMin` est un **seuil** sur le CSN (Calypso Serial Number, c'est-à-dire l'Application Serial Number, comparé comme entier non signé sur 64 bits) ;
- **par FCI** (`…ByFci(maxDuration: Long, fciRegex: String)`) : `fciRegex` est une expression régulière appliquée au **FCI complet** retourné par *Select Application* (hors mot d'état), représenté en hexadécimal majuscule sans séparateur.

- **Nouvelle interface parente `SecuritySettings`** (`calypso.card.transaction`), dont héritent `SymmetricCryptoSecuritySettings` et `AsymmetricCryptoSecuritySettings`. Elle porte les réglages communs à toute transaction sécurisée, quelle que soit la nature cryptographique de la session — quatre nouvelles opérations :
  - `assignOpenSecureSessionMaxDurationByCsn(maxDuration: Long, csnMin: Long) → Self` et `assignOpenSecureSessionMaxDurationByFci(maxDuration: Long, fciRegex: String) → Self` — durée maximale de l'échange de la commande *Open Secure Session* ;
  - `assignCloseSecureSessionMaxDurationByCsn(maxDuration: Long, csnMin: Long) → Self` et `assignCloseSecureSessionMaxDurationByFci(maxDuration: Long, fciRegex: String) → Self` — durée maximale de l'échange de la commande *Close Secure Session*.
- **`SymmetricCryptoSecuritySettings`** — deux nouvelles opérations spécifiques :
  - `assignSvCommandMaxDurationByCsn(maxDuration: Long, csnMin: Long) → Self` et `assignSvCommandMaxDurationByFci(maxDuration: Long, fciRegex: String) → Self` — durée maximale de l'échange de l'une des commandes *SV Reload*, *SV Debit* ou *SV Undebit*.

> **Règle de résolution** (pour une carte donnée, par type d'opération bornée) :
> 1. **Réglages par CSN** : chaque appel définit une **plage** délimitée par son `csnMin` et le `csnMin` immédiatement supérieur déclaré (ou +∞). Si le CSN de la carte appartient à une plage dont la `maxDuration` est différente de `Long.MAX_VALUE`, cette valeur s'applique.
> 2. **Réglages par FCI** : sinon, les réglages par FCI sont évalués **dans l'ordre de déclaration** et le premier dont l'expression correspond au FCI s'applique ; une `maxDuration` égale à `Long.MAX_VALUE` signifie alors « pas de borne ».
> 3. Sinon, aucune borne ne s'applique.

Conséquences et précisions :

- les réglages par CSN agissent comme des **dérogations** : une borne par défaut s'exprime par un dernier réglage par FCI d'expression `.*`, et non par un réglage par CSN au seuil le plus bas, qui masquerait tous les réglages par FCI ; `Long.MAX_VALUE` sur une plage de CSN rend la main aux réglages par FCI pour les cartes de cette plage ;
- la correspondance porte sur la **chaîne entière** (ancrage implicite aux deux extrémités) ; un octet correspond à `..` ;
- pour rester portable (Java, .NET, Swift/ICU, Rust), l'expression est limitée à un **sous-ensemble commun** : caractères littéraux, `.`, classes `[...]`, quantificateurs `*`, `+`, `?`, `{n}`, `{n,}`, `{n,m}`, alternative `|` et groupes `(...)` ; références arrière, assertions de voisinage, ancres et options en ligne sont exclues ;
- une expression invalide ou hors du sous-ensemble, ou une `maxDuration` non strictement positive, est rejetée **dès l'appel** (pré-condition de nature *Argument*) ;
- un nouvel appel avec un `csnMin` déjà déclaré remplace la valeur précédente ; un nouvel appel avec une `fciRegex` identique remplace la valeur en **conservant sa position** dans l'ordre d'évaluation ;
- sans FCI disponible, aucun réglage par FCI ne correspond ;
- **intégrité du FCI** : le FCI est obtenu lors de la sélection, hors session, donc sans authentification. Si l'expression filtre sur les données de la *startup info*, l'intégrateur **doit** exécuter un `prepareGetData(FCI_FOR_CURRENT_DF)` en session afin de s'assurer de l'intégrité du FCI obtenu lors de la sélection. `prepareGetData` est désormais autorisée en session **pour ce seul tag** ; si la valeur retournée diffère de la réponse au *Select Application*, l'erreur `InconsistentData` est levée par `processCommands` (et non par `prepareGetData`).

`maxDuration` est placé en tête, car c'est la valeur que l'opération assigne.

> **Durée mesurée** : chaque borne porte sur le **seul échange de la commande** concernée, de l'émission de la commande à la réception de sa réponse ; les autres commandes de la session sécurisée ou de l'opération SV ne sont pas comptées. **Conséquence d'un dépassement** : la session sécurisée ouverte est automatiquement annulée, et l'erreur remonte sous forme d'`InvalidCardResponse` ; hors session, seule l'erreur remonte (cf. §3.1).

> L'expression régulière sur le FCI couvre à elle seule le nom du DF, les informations de démarrage (familles de produits, masquage d'octets) et même un préfixe du numéro de série (tag `C7`), avec un ordre de priorité choisi par l'intégrateur. Cette forme remplace les critères `dfName` / `startupInfo` des versions de travail précédentes de ce document.

### 3.4 Generic Card API

- **`GenericCardTransactionManager.prepareCommandWithMaxDuration(commandId: Int, apdu: ByteArray, maxDuration: Long) → Self`** — prépare une commande avec un identifiant (cf. Thème 7) et une borne de durée. Si la durée effective dépasse la borne, l'erreur `InvalidCardResponse` identifie la commande fautive.

> La Generic Card API expose ainsi la contre-mesure relai **au niveau de chaque commande individuelle**, cohérent avec son modèle d'usage (séquences d'APDU sans transaction sécurisée explicite).

### 3.5 Storage Card API

- **Nouvelle classe de données `StorageCardSecuritySettings`** (`storagecard.transaction`), avec la propriété `readCommandMaxDurations: Map<StorageCardProductType, Long> = emptyMap()` : durée maximale, en microsecondes, de l'échange d'**une seule commande de lecture**, pour chaque type de produit. Un type absent n'est pas borné. Une même instance peut être partagée par toutes les transactions d'un terminal.
- **Opération de fabrique modifiée** : `createStorageCardTransactionManager(reader, card, securitySettings) → StorageCardTransactionManager`. Une instance par défaut de `StorageCardSecuritySettings` désactive tout bornage.
- **Portée** : la borne s'applique aux commandes de lecture préparées sur le gestionnaire de transaction (`prepareReadBlock`, `prepareReadBlocks`, `prepareSt25ReadSystemBlock`) ; elle ne s'applique ni à la sélection, ni aux écritures, ni à l'authentification. Un dépassement lève `SCInvalidCardResponse`, qui porte déjà `blockAddress` et `commandId`.

> **Menace visée** : pour les cartes de stockage, il ne s'agit pas du relais mais de l'**émulation de carte** par un matériel RFID générique, qui ne répond pas à une commande de lecture dans le même temps que la puce du produit attendu.

> Les cartes de stockage n'ont ni FCI ni session sécurisée : le **type de produit** suffit à segmenter le parc, là où la Calypso Card API utilise le CSN et le FCI.

### 3.6 Justification

Le relai comme l'émulation introduisent un écart de durée significatif et systématique sur les échanges APDU ; surveiller cet écart au niveau du lecteur (Card API), des commandes bornées d'une transaction Calypso (Calypso Card API), de chaque commande générique (Generic Card API) et de chaque lecture de carte de stockage (Storage Card API) couvre l'ensemble des scénarios d'usage des API Terminaux.

---

## 4. Thème 3 — Simplification de la gestion de l'observation

### 4.1 Motivation

Le modèle en production exposait un **patron Observateur** complet (`addObserver`, `removeObserver`, `clearObservers`, `countObservers`, `setReaderObservationExceptionHandler`) auquel s'ajoutaient deux SPI distinctes (`CardReaderObserverSpi` et `CardReaderObservationExceptionHandlerSpi`). En pratique, **un seul observateur** est enregistré, et la séparation entre gestionnaire d'événements et gestionnaire d'erreurs n'apportait pas de valeur.

### 4.2 Reader API — `ObservableCardReader`

**Opérations supprimées** :

- `setReaderObservationExceptionHandler(CardReaderObservationExceptionHandlerSpi)`
- `addObserver(CardReaderObserverSpi)`, `removeObserver(CardReaderObserverSpi)`, `clearObservers()`, `countObservers()`
- `startCardDetection(DetectionMode)` (signature à un argument)
- `finalizeCardProcessing()` (renommée, cf. ci-dessous)

**Opérations ajoutées / refondues** :

- `startCardDetection(settings: CardDetectionSettings, eventHandler: CardReaderEventHandler) → Unit` — l'enregistrement du gestionnaire se fait **au moment où la détection démarre**, en une seule opération, avec les paramètres de détection (cf. Thème 6).
- `endCardProcessing() → Unit` — remplace `finalizeCardProcessing()` ; idempotente ; libère en outre les références aux `SmartCard` de la dernière sélection (cf. §2.3.2).
- `clearScheduledCardSelectionScenario() → Unit` — **nouvelle opération** qui retire le scénario de sélection planifié sur le lecteur. À partir de l'insertion suivante, aucun scénario n'est exécuté et `CardReaderEvent.scheduledCardSelectionsResponse` vaut `null`. Idempotente.

**SPI supprimées** : `CardReaderObserverSpi`, `CardReaderObservationExceptionHandlerSpi`.

**SPI ajoutée** : `CardReaderEventHandler`, qui **fusionne** les deux SPI précédentes :

- `onReaderEvent(cardReaderEvent: CardReaderEvent) → Unit` ;
- `onReaderError(context: String, readerName: String, error: Any) → Unit`.

### 4.3 Justification

Cette simplification :

1. **réduit la surface de l'API** (5 opérations et 2 SPI deviennent 1 opération et 1 SPI) ;
2. **élimine les états invalides** (observateur enregistré sans gestionnaire d'erreurs, détection démarrée sans observateur, etc.) ;
3. **aligne l'API** sur l'usage réel observé chez les intégrateurs.

---

## 5. Thème 4 — Connaissance de l'état courant de la session sécurisée

### 5.1 Motivation

Côté Calypso, l'application n'avait pas de moyen direct de savoir **si une session sécurisée était ouverte**, ni d'en connaître la **nature** (symétrique / asymétrique) ou le **niveau d'accès en écriture**.

### 5.2 Calypso Card API

- **Nouvelle opération** `TransactionManager.getSecureSessionState() → SecureSessionState` — renvoie l'état de la session sécurisée au moment de l'appel.
- **Nouvelle énumération** `SecureSessionState` :
  - `NO_SESSION` — aucune session sécurisée n'est ouverte : aucune n'a encore été ouverte, ou la dernière a été fermée ou annulée ;
  - `ASYMMETRIC` — session PKI ;
  - `SYMMETRIC_PERSONALIZATION`, `SYMMETRIC_LOAD`, `SYMMETRIC_DEBIT` — session symétrique ouverte avec le niveau d'accès en écriture correspondant.

> **Évolution par rapport à la version de travail précédente de ce document** : le couple `SecureSessionStatus` (objet avec `isOpen`, `type`, `writeAccessLevel`) / `SecureSessionType` est remplacé par une énumération unique. L'ancienne forme laissait indéfinis `type` et `writeAccessLevel` lorsqu'aucune session n'était ouverte ; l'énumération rend ces combinaisons impossibles. Le nom *State* (plutôt que *Status*) désigne un état parmi des états exclusifs et évite toute confusion avec les données de statut renvoyées par la carte (status word, `dfStatus`, etc.).

> **Granularité retenue** : les valeurs reflètent la **nature cryptographique** de la session et non le **mode applicatif** (Regular / Extended) ; ce dernier se déduit du sous-type du `TransactionManager` instancié.

### 5.3 Justification

Le `TransactionManager` Calypso est désormais **introspectable** sur sa propre session sécurisée, ce qui évite à l'appelant de maintenir son propre suivi d'état.

---

## 6. Thème 5 — Améliorations sémantiques (renommages et suppressions)

Ce thème regroupe les renommages et suppressions motivés par la clarté ou la cohérence. Les changements qui découlent de la notation indépendante du langage (suffixes `Exception`, `Spi`, `Api`, énumérations imbriquées, surcharges) sont décrits au Thème 10 ; la liste exhaustive figure en annexe A.

### 6.1 Reader API

| Avant (Java 2.1.0) | Après (3.0.0) | Justification |
|---|---|---|
| `ObservableCardReader.DetectionMode.SINGLESHOT` | `DetectionMode.SINGLE_SHOT` | Convention `UPPER_SNAKE_CASE` (mot composé). |
| `CardReaderEvent.Type.UNAVAILABLE` | `CardReaderEventType.READER_UNREGISTERED` | Le nom décrit la **cause** réelle de l'événement. |
| `ObservableCardReader.NotificationMode` | `CardPresenceNotificationPolicy` (namespace `reader.selection`) | Le nom décrit ce qui est notifié ; la notion appartient à la **sélection**. |
| `ObservableCardReader.finalizeCardProcessing()` | `ObservableCardReader.endCardProcessing()` | `finalize` est chargé en Java (méthode d'`Object`, dépréciée par le JDK). |

**Suppressions** :

- `CardSelectionManager.setMultipleSelectionMode()` et `prepareReleaseChannel()` — cf. Thèmes 1 et 9 ;
- `ChannelControl` et `CardTransactionManager.processCommands(ChannelControl)` — remplacée par `processCommands()` ;
- `ReaderProtocolNotSupportedException` — disparaît avec `ConfigurableCardReader` (cf. Thème 6) ;
- `reader.selection.InvalidCardResponseException` — doublon de l'erreur de même nom du namespace `reader`.

### 6.2 Card API

- **Suppression** de `ChannelControl`, de `ProxyReaderApi.releaseChannel()` et de `CardResponseApi.isLogicalChannelOpen()` (cf. Thème 1).
- **Suppression** de l'erreur abstraite `AbstractApduException` : ses informations (`cardResponse`, `isCardResponseComplete`) sont portées directement par les quatre erreurs concernées (`ReaderBrokenCommunication`, `CardBrokenCommunication`, `UnexpectedStatusWord`, `ApduExchangeDurationExceeded`).

### 6.3 Calypso Card API

- **Suppressions** :
  - `TransactionManager.processCommands(ChannelControl)` et `ChannelControl` (dépréciés) ;
  - les erreurs `UnexpectedCommandStatusException`, `ReaderIOException`, `CardIOException` (dépréciées), couvertes par `InvalidCardResponse`, `ReaderCommunication` et `CardCommunication` de la Reader API ;
  - l'erreur `SelectFileException`, devenue sans objet (cf. Thème 13) ;
  - `CalypsoCardApiFactory.createSearchCommandData()` (cf. Thème 11).
- **Renommages** d'opérations surchargées (cf. Thème 10) : `prepareSelectFile(short)` → `prepareSelectFileByLid`, `prepareSelectFile(SelectFileControl)` → `prepareSelectFileByControl` (paramètre harmonisé `selectFileControl`), dans `CalypsoCardSelectionExtension` et `TransactionManager`.
- **Énumérations imbriquées renommées** : `CalypsoCard.ProductType` → `CalypsoCardProductType`, `ElementaryFile.Type` → `ElementaryFileType`.
- **Collections nommées au pluriel** : les paramètres `counterNumberToDecValueMap` / `counterNumberToIncValueMap` de `prepareDecreaseCounters` / `prepareIncreaseCounters` deviennent `decrementValues` / `incrementValues` ; les propriétés `kif` / `kvc` de `DirectoryHeader` deviennent `kifByAccessLevel` / `kvcByAccessLevel`.
- **Réglages de sécurité renommés au pluriel** : `SymmetricCryptoSecuritySetting` → `SymmetricCryptoSecuritySettings`, `AsymmetricCryptoSecuritySetting` → `AsymmetricCryptoSecuritySettings` ; les opérations de fabrique suivent (`createSymmetricCryptoSecuritySettings`, `createAsymmetricCryptoSecuritySettings`), ainsi que le paramètre `securitySettings` des opérations `createSecure…TransactionManager`.

### 6.4 Legacy SAM API

- **Suppressions** :
  - `TransactionManager.processCommands()` (déprécié) et `processCommands(ChannelControl)` : le gestionnaire hérite désormais de `CardTransactionManager.processCommands()` de la Reader API ;
  - les erreurs `UnexpectedCommandStatusException`, `ReaderIOException`, `SamIOException`, couvertes par les erreurs de la Reader API ;
  - `LegacySamRevocationServiceSpi.isSamRevoked(serialNumber)` (variante sans valeur de compteur) : seule subsiste `isSamRevoked(serialNumber: ByteArray, counterValue: Int) → Boolean`.
- **Renommages** d'opérations surchargées (cf. Thème 10) :

| Avant (Java 1.0.0) | Après (2.0.0) |
|---|---|
| `setUnlockData(String, LegacySam.ProductType)` | `setUnlockDataForProductType(unlockData, productType)` |
| `setStaticUnlockDataProvider(provider)` | `setStaticUnlockDataProviderWithDeferredReader(provider)` |
| `setStaticUnlockDataProvider(provider, targetSamReader)` | `setStaticUnlockDataProvider(provider, targetSamReader)` *(cas nominal, nom inchangé)* |
| `setDynamicUnlockDataProvider(provider)` | `setDynamicUnlockDataProviderWithDeferredReader(provider)` |
| `setDynamicUnlockDataProvider(provider, targetSamReader)` | `setDynamicUnlockDataProvider(provider, targetSamReader)` *(cas nominal, nom inchangé)* |
| `prepareReadWorkKeyParameters(int)` / `(byte, byte)` | `prepareReadWorkKeyParametersByRecordNumber` / `prepareReadWorkKeyParametersByKifKvc` |
| `getWorkKeyParameter(int)` / `(byte, byte)` | `getWorkKeyParametersByRecordNumber` / `getWorkKeyParametersByKifKvc` |
| `prepareTransferWorkKeyDiversified(…, diversifier)` | `prepareTransferWorkKeyDiversifiedWithSpecificDiversifier(…, diversifier)` |
| `LegacySam.ProductType` | `LegacySamProductType` |

### 6.5 Generic Card API

| Avant (Java 1.0.0) | Après (2.0.0) | Justification |
|---|---|---|
| `CardTransactionManager` | `GenericCardTransactionManager` | Nom propre à l'API, sans collision avec `CardTransactionManager` de la Reader API dont il hérite. |
| `GenericCardApiFactory.createCardTransaction(reader, card)` | `createGenericCardTransactionManager(reader, card)` | Le nom désigne l'objet créé. |
| `prepareApdu(String)` | *(supprimée)* | La conversion depuis une chaîne est laissée à l'application. |
| `prepareApdu(byte[])` | `prepareCommand(apdu: ByteArray)` | Nom orienté « commande ». |
| `prepareApdu(byte cla, byte ins, byte p1, byte p2, byte[] dataIn, Byte le)` | *(supprimée)* | La construction d'APDU par champs est laissée à l'application. |
| `getResponsesAsByteArrays()` | `getLastExecutionResponses() → List<ByteArray>` | Précise la **portée temporelle** (dernière exécution). |
| `getResponsesAsHexStrings()` | *(supprimée)* | La représentation hexadécimale est laissée à l'application. |

Nouvelles opérations liées aux Thèmes 2 et 7 : `prepareCommandWithId`, `prepareCommandWithMaxDuration`, `getLastExecutionResponse` (cf. §8.2).

### 6.6 Storage Card API

| Avant (Java 1.2.0) | Après (2.0.0) | Justification |
|---|---|---|
| `ProductType` (namespace `storagecard.card`) | `StorageCardProductType` | Nom propre à l'API, sans ambiguïté avec les autres types de produit de la famille. |
| `ProductType.getBlockCount()`, `getBlockSize()`, `hasSystemBlock()`, `hasWriteAcknowledgment()`, `hasAuthentication()` | propriétés `blockCount`, `blockSize`, `hasSystemBlock`, `hasWriteAcknowledgment`, `hasAuthentication` de l'énumération | Ce sont des données propres à chaque produit, exposées comme telles (cf. Thème 10). |
| `StorageCard.getUID()` | `StorageCard.getUid()` | Convention `lowerCamelCase` des acronymes. |
| `prepareMifareClassicAuthenticate(…, byte[] key)` | `prepareMifareClassicAuthenticateWithKey(…, key)` | Noms d'opérations uniques (cf. Thème 10). |
| `prepareMifareClassicAuthenticate(…, int keyNumber)` | `prepareMifareClassicAuthenticateWithKeyNumber(…, keyNumber)` | (idem) |
| `StorageCardTransactionManager.prepareReadSystemBlock()`, `prepareWriteSystemBlock(byte[])` *(dépréciées)* | *(supprimées)* ; subsistent `prepareSt25ReadSystemBlock()` et `prepareSt25WriteSystemBlock(commandId, data)` | Le préfixe `St25` reflète la nature produit-spécifique du bloc système. |
| Interface `StorageCardException` (`getBlockAddress()`) | *(supprimée)* ; les erreurs portent `blockAddress: Int?` et `commandId: Int?` | Les informations sont portées directement par chaque erreur. |
| `SCAuthenticationFailedException extends CardCommunicationException` | `SCAuthenticationFailed` *(sans erreur parente)* | Un échec d'authentification n'est pas une erreur de communication. |

Par ailleurs, `StorageCard.getBlock`, `getBlocks` et `getSystemBlock` renvoient désormais explicitement `ByteArray?` (`null` si la donnée n'a pas été lue).

### 6.7 Crypto Symmetric et Crypto Asymmetric APIs

Les évolutions de ces deux APIs relèvent des Thèmes 10 et 11 (objets de données, suppression des objets d'entrée/sortie) ; elles sont détaillées aux §11 et §12 et en annexe A.

---

## 7. Thème 6 — Typage strict des technologies RF et des types de carte (support ECP)

### 7.1 Motivation

Deux moteurs convergent :

1. **Fin des chaînes libres pour les protocoles**. Les versions en production configuraient l'activation de protocoles et le filtrage de sélection à l'aide de **chaînes de caractères** (`physicalProtocolName`, `logicalProtocolName`) : valeurs non normalisées, fautes de frappe non détectées, documentation dispersée.
2. **Arrivée du support ECP** (Enhanced Contactless Polling), mécanisme défini par la **spécification Apple ECP** permettant la détection rapide de cartes (notamment Calypso) en mode transit sur iPhone, qui nécessite l'envoi d'une **trame de polling spécifique** au démarrage de la détection. La trame est traitée comme une donnée binaire opaque construite par l'application.

### 7.2 Nouvelle API socle — Terminal Reader Definitions API

Les énumérations `RfTechnology` et `CardType` sont placées dans une **nouvelle API socle**, la **Terminal Reader Definitions API**, qui n'expose ni interface de service, ni SPI ; sa seule vocation est d'héberger les **types énumérés transverses** partagés entre APIs Terminaux.

#### Conséquences structurelles

- **Nouveau dépôt** : `calypsonet-terminal-reader-definitions-uml-api` (version `1.0.0-SNAPSHOT`).
- **Nouveau module Java Keypop** : `keypop-reader-definitions-jvm-api` (à créer).
- **Dépendance** publique et transitive de la Terminal Reader API vers la Terminal Reader Definitions API (les énumérations apparaissent dans les propriétés `BasicCardSelector.cardType`, `IsoCardSelector.cardType`, `CardDetectionSettings.rfTechnologies` et `…CardSelectionResult.cardType`).

#### Contenu initial

- **`ReaderDefinitionsApiProperties`** — constante `VERSION` du module ;
- **`RfTechnology`** : `ISO_14443_AB`, `INNOVATRON_B_PRIME`, `FELICA`, `ISO_15693` ;
- **`CardType`** : `ISO_7816_3`, `ISO_14443_4`, `ISO_14443_3A_MIFARE_CLASSIC_1K`, `ISO_14443_3A_MIFARE_CLASSIC_4K`, `ISO_14443_3A_MIFARE_ULTRALIGHT`, `ISO_14443_3B_ST25_SRT512`, `INNOVATRON_B_PRIME`, `FELICA`, `ISO_15693`, `UNKNOWN`.

> **Granularité de `CardType` et asymétrie A/B avec `RfTechnology`** : `RfTechnology` est une **donnée d'entrée** (polling) qui fusionne A et B au niveau ISO 14443 ; `CardType` est une **donnée de sortie** qui combine niveau de protocole et identité produit — une seule valeur `ISO_14443_4` pour le transport, mais une granularité produit (`ISO_14443_3A_…`, `ISO_14443_3B_…`) pour les produits propriétaires.
>
> **`CardType.UNKNOWN`** est renvoyée lorsque le type de la carte n'a pas pu être identifié ; utilisée comme filtre de sélection, elle permet de **capturer explicitement** ces cartes.

### 7.3 Reader API — typage strict

- **Supprimée** : `CardSelector.filterByCardProtocol(String logicalProtocolName)`.
- **Ajoutée** : la propriété `cardType: CardType? = null` des sélecteurs `BasicCardSelector` et `IsoCardSelector` — filtre par type de carte (`null` désactive le filtre).
- **Supprimée intégralement** : l'interface `ConfigurableCardReader` et ses opérations `activateProtocol(String, String)`, `deactivateProtocol(String)`, `getCurrentProtocol()`.

### 7.4 Reader API — paramètres de détection

Toutes les informations de détection sont regroupées dans la **classe de données** `CardDetectionSettings`, construite directement par l'application :

| Propriété | Type | Valeur par défaut | Rôle |
|---|---|---|---|
| `detectionMode` | `DetectionMode` | `DetectionMode.REPEATING` | reprise ou non de la détection après chaque traitement de carte |
| `rfTechnologies` | `Set<RfTechnology>` | `setOf(RfTechnology.ISO_14443_AB)` | technologies RF activées pendant le polling (sans effet sur un lecteur à contact) |
| `ecpFrame` | `ByteArray?` | `null` | trame ECP émise au démarrage du polling (lecteurs ECP uniquement) |

- `DetectionMode` devient une énumération **de premier niveau** du namespace `reader` (elle était imbriquée dans `ObservableCardReader`).
- **Tolérance des paramètres non supportés** : un paramètre non supporté par le lecteur (technologie RF, trame ECP) est **ignoré silencieusement** et un message de niveau `WARN` est typiquement journalisé ; **aucune erreur** n'est levée.

> **Évolution par rapport à la version de travail précédente de ce document** : `CardDetectionSettings` n'est plus une interface « builder » obtenue par `ReaderApiFactory.createCardDetectionSettings()`, mais une classe de données à valeurs par défaut (cf. Thème 10).

### 7.5 Reader API — `ObservableCardReader` et résultat de sélection

- `startCardDetection(settings: CardDetectionSettings, eventHandler: CardReaderEventHandler) → Unit` transporte en un seul appel le gestionnaire d'événements et la configuration de détection.
- Le type de carte détecté est exposé par la propriété **`cardType: CardType`** des résultats de sélection (`SingleCardSelectionResult`, `MultipleCardSelectionResult`, `MultichannelCardSelectionResult`, cf. Thème 9) ; `UNKNOWN` si le type n'a pas pu être identifié.

### 7.6 Justification

- **Sécurité de compilation côté valeurs** : le passage des chaînes aux énumérations élimine une classe de bugs et rend l'API auto-descriptive.
- **Simplicité** : un seul `ObservableCardReader`, une seule classe de paramètres de détection.
- **Évolutivité** : ajouter un paramètre de polling revient à ajouter une propriété à valeur par défaut à `CardDetectionSettings`, sans toucher la signature de `startCardDetection`.
- **Séparation entrée / sortie** : `RfTechnology` en entrée (polling), `CardType` en sortie (résultat) et comme critère de sélection.
- **Déclaration unique et typée** : en production, le protocole devait être déclaré deux fois (`activateProtocol` côté lecteur, `filterByCardProtocol` côté sélecteur) ; il n'est plus déclaré qu'une fois, de manière typée.
- **Réutilisabilité transverse** grâce à l'extraction des énumérations dans la Terminal Reader Definitions API.

---

## 8. Thème 7 — Identification des commandes (`commandId`)

### 8.1 Motivation

Plusieurs APIs permettent de **préparer plusieurs commandes** avant de les exécuter en bloc. En production, l'application n'avait pas de moyen direct d'identifier **quelle commande** avait posé problème, ni d'accéder au **résultat d'une commande précise** ; certaines APIs recouraient pour cela à des objets conteneurs mutables (`KeyPairContainer`, `SearchCommandData.getMatchingRecordNumbers()`, `SignatureComputationData.getSignature()`, etc.).

Les nouvelles versions généralisent un mécanisme unique : un **identifiant entier `commandId` fourni par l'application** au moment de la préparation, puis utilisé pour retrouver le résultat de la commande ou pour identifier la commande fautive. Le paramètre `commandId`, **toujours placé en première position**, suit la même convention que `selectionId` dans la Reader API (cf. Thème 9).

> Le nom `commandId` remplace `idCommand` de la version de travail précédente de ce document, par cohérence avec `selectionId`.

### 8.2 Generic Card API

- `prepareCommandWithId(commandId: Int, apdu: ByteArray) → Self` ;
- `prepareCommandWithMaxDuration(commandId: Int, apdu: ByteArray, maxDuration: Long) → Self` (cf. Thème 2) ;
- `getLastExecutionResponse(commandId: Int) → ByteArray?` — réponse de la commande identifiée ; si plusieurs commandes partagent le même identifiant, c'est la plus récemment traitée qui est renvoyée.
- `prepareCommand(apdu: ByteArray) → Self` reste disponible pour les commandes non identifiées.

### 8.3 Storage Card API

- `prepareWriteBlocks(commandId: Int, fromBlockAddress: Int, data: ByteArray) → Self` ;
- `prepareSt25WriteSystemBlock(commandId: Int, data: ByteArray) → Self`.

Pour ces deux opérations d'écriture, **`commandId` est obligatoire** (non nullable). Il est restitué par la propriété `commandId: Int?` des erreurs de la Storage Card API lorsque la commande identifiée en est la cause (`null` pour les commandes qui n'en portent pas, comme les lectures).

### 8.4 Calypso Card API

- `prepareSearchRecords(commandId: Int, data: SearchCommandData) → Self` ;
- `CalypsoCard.getMatchingRecordNumbers(commandId: Int) → List<Int>?` — numéros des enregistrements trouvés par la recherche identifiée. Cette opération remplace `SearchCommandData.getMatchingRecordNumbers()` (cf. Thème 11).

### 8.5 Legacy SAM API

Les résultats des commandes cryptographiques ne sont plus lus sur des objets conteneurs mais sur la `LegacySam`, par identifiant :

| Préparation | Lecture du résultat |
|---|---|
| `FreeTransactionManager.prepareGenerateCardAsymmetricKeyPair(commandId: Int)` | `LegacySam.getKeyPair(commandId: Int) → ByteArray?` |
| `FreeTransactionManager.prepareComputeCardCertificate(commandId: Int, data: LegacyCardCertificateComputationData)` | `LegacySam.getComputedCardCertificate(commandId: Int) → ByteArray?` |
| `prepareComputeSignature(commandId: Int, data: SignatureComputationData)` (sur `FreeTransactionManager` et `CardTransactionLegacySamExtension`) | `LegacySam.getSignature(commandId: Int) → ByteArray?` et, en mode traçable, `LegacySam.getSignedData(commandId: Int) → ByteArray?` |
| `prepareVerifySignature(commandId: Int, data: SignatureVerificationData)` (idem) | `LegacySam.isSignatureValid(commandId: Int) → Boolean?` |

Ces lectures remplacent `KeyPairContainer.getKeyPair()`, `LegacyCardCertificateComputationData.getCertificate()`, `SignatureComputationData.getSignature()`, `TraceableSignatureComputationData.getSignedData()` et `SignatureVerificationData.isSignatureValid()`.

### 8.6 Justification

- **Sémantique applicative** : l'identifiant est **choisi par l'application**, qui peut l'aligner sur sa propre logique métier.
- **Objets de données immuables** : les paramètres d'une commande deviennent de pures données (cf. Thème 10) ; le résultat est porté par l'objet vivant qui reçoit les réponses (`CalypsoCard`, `LegacySam`, gestionnaire de transaction), comme toutes les autres données issues de la carte.
- **Cohérence inter-APIs** : le même patron (identifiant en tête, lecture par identifiant) s'applique à toutes les APIs concernées.

---

## 9. Thème 8 — Découverte et accès normalisés aux lecteurs (`CardReaderProvider`)

### 9.1 Motivation

En production, la Reader API n'exposait aucun moyen normalisé de **découvrir** les lecteurs disponibles ni d'**obtenir une référence sur un lecteur**. L'application devait s'appuyer sur les abstractions plugin et pool du framework Keyple sous-jacent, propres à l'implémentation et non portables.

### 9.2 Reader API

- **Nouvelle opération** `ReaderApiFactory.getCardReaderProvider() → CardReaderProvider` — renvoie le `CardReaderProvider` de l'environnement d'exécution ; des appels successifs renvoient la même instance.
- **Nouvelle interface** `CardReaderProvider` :
  - `getReaderNames() → Set<String>` ;
  - `getReaders() → Set<CardReader>` ;
  - `getReader(readerName: String) → CardReader?` — correspondance exacte du nom, `null` si aucun lecteur ne correspond ;
  - `findReader(readerNameRegex: String) → CardReader?` — premier lecteur dont le nom correspond à l'expression régulière, `null` sinon.

Le cycle de vie des lecteurs reste piloté par l'environnement d'exécution ; `CardReaderProvider` est une **vue en lecture seule** sur les lecteurs actifs à chaque appel.

### 9.3 Justification

- **Découplage des concepts d'implémentation Keyple** : l'application ne connaît plus que la Reader API.
- **Citoyen de premier niveau de l'API publique** pour un besoin universel.
- **Deux modes de recherche complémentaires** : nom exact et expression régulière.

---

## 10. Thème 9 — Refonte du modèle de sélection de cartes

### 10.1 Motivation

En production, le `CardSelectionManager` réunissait tous les modes de sélection, pilotés par un effet de bord (`setMultipleSelectionMode()`), et renvoyait un unique `CardSelectionResult` dont certaines propriétés se déduisaient d'autres (`getActiveSmartCard()` et `getActiveSelectionIndex()` à partir de `getSmartCards()`). L'ajout du multicanal rendait ce résultat unique ambigu (quelle carte est « la » carte active lorsque plusieurs sont actives ?). La version de travail précédente de ce document proposait un paramètre `SelectionExecutionPolicy` ; cette piste a été abandonnée au profit d'un **type de gestionnaire par mode de sélection**.

### 10.2 Reader API — un gestionnaire par mode

Le mode de sélection n'est plus un paramètre : il est porté par le **type du gestionnaire**, chacun obtenu par sa propre opération de factory et produisant son propre type de résultat.

| Gestionnaire | Comportement | Exécution | Résultat |
|---|---|---|---|
| `SingleCardSelectionManager` | mono-canal ; s'arrête à la première sélection réussie | explicite ou planifiée | `SingleCardSelectionResult` |
| `MultipleCardSelectionManager` | mono-canal ; traite toutes les sélections, quels que soient les succès intermédiaires | explicite ou planifiée | `MultipleCardSelectionResult` |
| `MultichannelCardSelectionManager` | cartes ISO 7816-4 multicanal ; chaque sélection réussie occupe son propre canal logique | explicite | `MultichannelCardSelectionResult` |

- **Factory** : `createCardSelectionManager()` est remplacée par `createSingleCardSelectionManager()`, `createMultipleCardSelectionManager()` et `createMultichannelCardSelectionManager()`.
- **`CardSelectionManager`** devient l'interface commune et ne conserve que les opérations indépendantes du mode :
  - `prepareSelection(selectionId: Int, cardSelector: CardSelector, cardSelectionExtension: CardSelectionExtension) → Self` — l'identifiant de la sélection est **choisi par l'application** (au lieu d'un index renvoyé par l'API) ; il doit être unique dans le scénario ; les sélections sont exécutées dans l'ordre de préparation ;
  - `exportCardSelectionScenario() → String` ;
  - `importCardSelectionScenario(cardSelectionScenario: String) → Self` — **remplace** le scénario courant (au lieu de renvoyer l'index de la dernière sélection importée) ;
  - `exportProcessedCardSelectionScenario() → String`.
- **Opérations propres à chaque gestionnaire** (typées par leur résultat) : `processCardSelectionScenario`, `scheduleCardSelectionScenario` et `parseScheduledCardSelectionsResponse` (mono-canal uniquement), `importProcessedCardSelectionScenario` (le scénario traité importé doit provenir d'un gestionnaire du même type).
- `scheduleCardSelectionScenario(observableCardReader: ObservableCardReader, cardPresenceNotificationPolicy: CardPresenceNotificationPolicy) → Unit` n'a plus de paramètre de politique d'exécution.

#### Résultats de sélection

| Résultat | Propriétés |
|---|---|
| `SingleCardSelectionResult` | `cardType: CardType`, `selectionId: Int?`, `smartCard: SmartCard?` (`null` ensemble si aucune sélection n'a réussi) |
| `MultipleCardSelectionResult` | `cardType: CardType`, `smartCards: Map<Int, SmartCard>`, `activeSelectionId: Int?` (seule la carte de la dernière sélection réussie reste active) |
| `MultichannelCardSelectionResult` | `cardType: CardType`, `smartCards: Map<Int, SmartCard>` (toutes actives, une par canal) |

`CardSelectionResult` (avec `getSmartCards()`, `getActiveSmartCard()`, `getActiveSelectionIndex()`) et `SelectionExecutionPolicy` disparaissent.

#### Sélecteurs

- `BasicCardSelector` et `IsoCardSelector` deviennent des **classes de données** construites directement par l'application (au lieu d'interfaces « builder » créées par `ReaderApiFactory.createBasicCardSelector()` / `createIsoCardSelector()`, supprimées) :
  - `BasicCardSelector` : `cardType: CardType? = null`, `powerOnDataRegex: String? = null` ;
  - `IsoCardSelector` : les mêmes, plus `dfName: ByteArray? = null`, `fileOccurrence: FileOccurrence = FileOccurrence.FIRST`, `fileControlInformation: FileControlInformation = FileControlInformation.FCI`.
- `CardSelector<T>` devient une **interface scellée** sans membre, dont les seules implémentations sont ces deux sélecteurs.
- L'interface intermédiaire `CommonIsoCardSelector<T>` est supprimée ; ses énumérations imbriquées deviennent `FileOccurrence` et `FileControlInformation` (namespace `reader.selection`).
- `filterByDfName(String)` (AID en hexadécimal) est supprimée : `dfName` est un `ByteArray`.

### 10.3 Justification

- **Aucun effet de bord** : le mode de sélection est fixé à la création du gestionnaire.
- **Résultats sans ambiguïté** : chaque résultat n'expose que ce qui a du sens dans son mode ; les propriétés déductibles et la contradiction « carte du canal 0 » / « premier index actif » disparaissent.
- **Identifiants choisis par l'application**, cohérents avec `commandId` (Thème 7).
- **Nommage** : *Single* et *Multiple* désignent le nombre de sélections réussies conservées dans le résultat, pas un nombre de cartes ; toutes les sélections d'un scénario portent sur la même carte.

---

## 11. Thème 10 — Spécification indépendante du langage d'implémentation

### 11.1 Motivation

Les APIs Terminaux étaient jusqu'ici définies par des interfaces Java. Ce modèle liait de fait les APIs à la JVM et freinait leur mise en œuvre dans d'autres environnements (applications mobiles natives, systèmes embarqués, terminaux non Java). La nouvelle proposition de conception a pour objectif d'**élargir le choix des langages d'implémentation** : **Kotlin Multiplatform (KMP)**, **Rust**, **Swift**, **C#**, etc., en plus de Java. Les nouvelles spécifications sont donc rédigées dans une **notation indépendante du langage**, inspirée de Kotlin, afin que chaque binding traduise le contrat dans la forme la plus idiomatique de sa plateforme. Cette notation entraîne des changements de forme systématiques, décrits ci-dessous ; **la plupart n'affectent pas le comportement**, mais tous affectent la manière d'écrire le code appelant.

### 11.2 Principes de notation

| Principe | En production (Java) | Dans les spécifications | Exemples |
|---|---|---|---|
| **Types de base** | `byte`, `short`, `int`, `long`, `boolean`, `byte[]`, `Integer`… | `Byte`, `Short`, `Int`, `Long`, `Boolean`, `ByteArray`, `Int?`… (table *Data types* de chaque spec) | — |
| **Nullabilité explicite** | valeurs `null` documentées en Javadoc | type `T?` ; sinon la valeur n'est jamais `null` | `getFileBySfi(sfi: Byte) → ElementaryFile?` |
| **Chaînage fluide** | généricité récursive `T extends X<T>` | type de retour **`Self`** ; plus aucune généricité récursive | `TransactionManager.prepareReadRecords(...) → Self` |
| **Classes de données** | interfaces « builder » (setters) ou interfaces de lecture (getters), créées par la factory | **classes de données** immuables à propriétés (`val`) et valeurs par défaut, construites directement par l'application ; les opérations `create…` correspondantes disparaissent de la factory | `SearchCommandData`, `BasicCardSelector`, `CardDetectionSettings`, `ApduRequest` |
| **Énumérations** | énumérations imbriquées dans une interface | énumérations **de premier niveau**, nommées de manière autonome | `CardReaderEvent.Type` → `CardReaderEventType` |
| **Données propres à une valeur d'énumération** | méthodes de l'énumération | **propriétés** de l'énumération | `StorageCardProductType.blockSize` |
| **Types fermés** | interface générique ouverte | **interface scellée** | `CardSelector`, `SignatureComputationData`, `SignatureVerificationData` |
| **Interfaces sans opération** | interface vide | **interface marqueur** | `ScheduledCardSelectionsResponse`, `CardSelectionExtension` |
| **Erreurs** | classes `…Exception` (vérifiées ou non) | erreurs nommées **sans suffixe**, portant `message: String` et `cause: Any?` | `CardCommunicationException` → `CardCommunication` |
| **Suffixes techniques** | `…Spi`, `…Api` sur les types de données de la Card API et des APIs crypto | supprimés pour les **données** ; conservés pour les interfaces de contrat | `ApduRequestSpi` → `ApduRequest`, `SvCommandSecurityDataApi` → `SvCommandSecurityData` |
| **Noms d'opérations uniques** | surcharges (même nom, paramètres différents) | **un nom unique par opération** dans une interface et sa hiérarchie ; suffixes `By…`, `With…`, `For…` | `prepareSelectFile` → `prepareSelectFileByLid` / `prepareSelectFileByControl` |
| **Type universel** | `Object`, `Throwable` | `Any` | `onReaderError(context, readerName, error: Any)` |
| **Réflexion** | `Class<E>` | supprimée | `getCryptoExtension(Class<E>)` → `getCryptoExtension()` (cf. Thème 14) |
| **Sérialisation** | `extends Serializable` | supprimée de la notation | `ApduResponseApi`, `CardResponseApi` |

### 11.3 Contrats normatifs

Chaque opération est désormais décrite par une table normative qui précise :

- sa **signature**, sa version d'introduction (*Since*) et sa **description** ;
- ses **pré-conditions**, chacune préfixée par sa nature, qui détermine l'erreur levée si elle n'est pas respectée : *Argument* (argument invalide, y compris une valeur hors des bornes du protocole de la carte ou du SAM), *Range* (position hors des bornes d'une collection ou d'une image mémoire exposée par l'API), *State* (état illégal), *Capability* (opération non supportée) ;
- ses **erreurs**, c'est-à-dire les situations qu'un appelant correct doit traiter ;
- des **renvois** vers les opérations et types liés.

Les spécifications définissent en outre des règles communes (résultats non `null` sauf indication contraire, collections vides plutôt que `null`, instances non partagées entre threads, valeurs par défaut des paramètres) ainsi que, pour la Card API et les APIs crypto, des **clauses de conformité** qui établissent la correspondance entre leurs types et ceux de la Reader API ou de la Calypso Card API (par exemple, tout objet implémentant `ProxyReaderApi` doit aussi implémenter `CardReader`).

### 11.4 Justification

- **Portabilité** : le contrat n'impose plus de mécanisme propre à Java (réflexion, généricité récursive, surcharges) ; il est transposable dans tout langage, y compris ceux qui n'ont ni surcharge ni héritage de classes (Rust notamment), ce qui ouvre la voie à des implémentations en Kotlin Multiplatform, Rust, Swift ou C#.
- **Robustesse** : la nullabilité explicite et les données immuables éliminent des états invalides (objet « builder » incomplet, valeur `null` inattendue).
- **Précision** : les pré-conditions typées et les erreurs listées par opération rendent le contrat vérifiable.
- **Binding Java** : la manière dont le binding Java réalisera ces principes (classes de données, `Self`, valeurs par défaut) relève de l'alignement des modules Keypop (cf. §19.3) et du guide de migration.

---

## 12. Thème 11 — Données exposées sans calcul et accès aux données brutes

### 12.1 Motivation

Plusieurs types de données des versions en production mêlaient **données** et **calculs** (décodage d'un compteur à partir d'un enregistrement, sélection d'un paramètre par numéro), exposaient simultanément une valeur brute et ses champs décodés, ou servaient à la fois d'entrée et de sortie. Les spécifications appliquent trois règles :

1. **aucune opération calculatoire** sur un type de données : ces calculs sont réalisés par l'objet vivant qui détient les données (`CalypsoCard`, `LegacySam`) ;
2. **aucun champ déductible** d'autres champs dans une classe de données ;
3. **aucun objet d'entrée/sortie** : les entrées sont des paramètres, les sorties des valeurs renvoyées.

### 12.2 Calypso Card API

- **`FileData` est supprimée**, avec ses opérations `getContent()`, `getContent(numRecord)`, `getContent(numRecord, dataOffset, dataLength)`, `getAllRecordsContent()`, `getContentAsCounterValue(numCounter)` et `getAllCountersValue()` :
  - les enregistrements sont exposés directement par la propriété `ElementaryFile.records: SortedMap<Int, ByteArray>` (à la place de `ElementaryFile.getData()`) ;
  - les valeurs de compteurs sont obtenues par `CalypsoCard.getCounterValuesBySfi(sfi: Byte) → SortedMap<Int, Int>?` et `CalypsoCard.getCounterValuesByLid(lid: Short) → SortedMap<Int, Int>?`.
- **`DirectoryHeader`** : `getKif(WriteAccessLevel)` et `getKvc(WriteAccessLevel)` deviennent les propriétés `kifByAccessLevel: Map<WriteAccessLevel, Byte>` et `kvcByAccessLevel: Map<WriteAccessLevel, Byte>`.
- **`SearchCommandData`** devient une classe de données d'entrée (`sfi`, `searchData`, `startAtRecord = 1`, `offset = 0`, `repeatedOffset = false`, `mask: ByteArray? = null`, `fetchFirstMatchingResult = false`) ; le résultat est lu par `CalypsoCard.getMatchingRecordNumbers(commandId)` (cf. Thème 7) ; `CalypsoCardApiFactory.createSearchCommandData()` disparaît.
- **`SvLoadLogRecord` et `SvDebitLogRecord`** deviennent des classes de données sans la propriété `rawData`, redondante avec les champs décodés. Les valeurs brutes restent accessibles par trois nouvelles opérations de `CalypsoCard` : `getSvLoadLogRecordRawData() → ByteArray?`, `getSvDebitLogLastRecordRawData() → ByteArray?` et `getSvDebitLogAllRecordsRawData() → List<ByteArray>` ; chaque objet décodé renvoyé correspond au décodage de la valeur brute au moment de l'appel.
- `DirectoryHeader`, `ElementaryFile` et `FileHeader` deviennent des classes de données.

### 12.3 Legacy SAM API

- **`KeyParameter`** devient la classe de données **`KeyParameters`**, renommée au pluriel comme les getters `getSystemKeyParameters` / `getWorkKeyParameters…` qui la renvoient (`kif`, `kvc`, `algorithm`, `parameterValues: SortedMap<Int, Byte>`), sans `rawData` ; `getParameterValue(parameterNumber)` est remplacée par la propriété `parameterValues`. Les valeurs brutes sont accessibles par `LegacySam.getSystemKeyParametersRawData(systemKeyType)`, `getWorkKeyParametersRawDataByRecordNumber(recordNumber)` et `getWorkKeyParametersRawDataByKifKvc(kif, kvc)`.
- **`SamParameters`** est supprimée : `LegacySam.getSamParameters()` renvoie directement `ByteArray?`.
- **Compteurs** : `getCounter(counterNumber)` et `getCounterCeiling(counterNumber)` sont supprimées (les tables `getCounters()` et `getCounterCeilings()` suffisent) ; `getCounterIncrementAccess(counterNumber)` est remplacée par `getCounterIncrementAccesses() → SortedMap<Int, CounterIncrementAccess>`.
- **Données de commande** : `LegacyCardCertificateComputationData`, `BasicSignatureComputationData`, `TraceableSignatureComputationData`, `BasicSignatureVerificationData` et `TraceableSignatureVerificationData` deviennent des classes de données d'entrée (propriétés et valeurs par défaut au lieu de setters ; `withSamTraceabilityMode(offset, mode)` devient `samTraceabilityMode` / `traceabilityOffset`, `withoutBusyMode()` devient `busyMode = false`) ; leurs résultats sont lus sur la `LegacySam` par `commandId` (cf. §8.5). `KeyPairContainer` est supprimée. Les opérations `create…Data()` et `createKeyPairContainer()` disparaissent de `LegacySamApiFactory`.
- **`SecuritySetting`** devient la classe de données **`SecuritySettings`** (`samReader`, `controlSam`), renommée au pluriel comme les réglages de sécurité de la Calypso Card API, à la place de `setControlSamResource(samReader, controlSam)` ; `LegacySamApiFactory.createSecuritySetting()` disparaît.

### 12.4 Card API

- **`ApduResponse`** ne conserve que `apdu` (et `apduExchangeDuration`) : `getDataOut()` et `getStatusWord()` sont supprimées, car déductibles de `apdu`.
- `ApduRequestSpi`, `CardRequestSpi`, `CardSelectionRequestSpi`, `ApduResponseApi`, `CardResponseApi` et `CardSelectionResponseApi` deviennent les classes de données `ApduRequest`, `CardRequest`, `CardSelectionRequest`, `ApduResponse`, `CardResponse` et `CardSelectionResponse`. Les valeurs par défaut sont explicites : `successfulStatusWords = setOf(0x9000)`, `successfulSelectionStatusWords = setOf(0x9000)`, `info = null`, `cardRequest = null`.

### 12.5 Crypto Symmetric API

- **`SvCommandSecurityDataApi`** (objet d'entrée/sortie) est remplacé :
  - les entrées deviennent des paramètres : `computeSvCommandSecurityData(svGetRequest: ByteArray, svGetResponse: ByteArray, svCommandPartialRequest: ByteArray) → SvCommandSecurityData` ;
  - la sortie est la classe de données `SvCommandSecurityData` (`serialNumber`, `transactionNumber`, `terminalChallenge`, `terminalSvMac`, non nullables).
- `createCardTransactionManager(..., transactionAuditData: MutableList<ByteArray>)` : la liste d'audit est explicitement **modifiable** (le module crypto y ajoute ses données).
- `cipherPinForPresentation` et `cipherPinForModification` prennent `kif: Byte` et `kvc: Byte` non nullables (au lieu de `Byte` boxés).

### 12.6 Crypto Asymmetric API

- **`CaCertificateContentSpi`** devient la classe de données `CaCertificateContent` ; la propriété `isAidCheckRequested` est supprimée, car déductible de `aid` (`null` lorsque la vérification de l'AID n'est pas demandée).
- **`CardPublicKeySpi`** est supprimée : la clé publique de la carte est un `ByteArray` (`CardCertificateSpi.checkCertificateAndGetPublicKey(...) → ByteArray`, `AsymmetricCryptoCardTransactionManagerSpi.initTerminalPkiSession(cardPublicKey: ByteArray)`).
- Une **clause de conformité** établit la correspondance entre les SPI de cette API et les interfaces marqueurs de la Calypso Card API (`PcaCertificate`, `CaCertificate`, `CardCertificate`, les parsers et la factory).

### 12.7 Justification

- **Une API décrit des données et des comportements, pas des algorithmes de décodage** : les calculs sur les données restent sous la responsabilité de l'objet qui les détient.
- **Pas de double vérité** : un champ déductible peut diverger de sa source ; le supprimer élimine le risque.
- **Accès brut préservé** là où il a un usage réel (transmission au back-office, réinjection des paramètres de clé dans les commandes de transfert).

---

## 13. Thème 12 — Opérations Stored Value (SV)

### 13.1 Motivation

La spécification de la carte Calypso définit trois commandes SV — *Reload*, *Debit* et *Undebit* — précédées d'une commande *SV Get* dont un paramètre indique l'opération visée : *Reload* ou *Debit/Undebit*. Le modèle en production introduisait en plus une notion DO/UNDO (`SvAction`) qui n'existe pas pour le rechargement et masquait l'annulation de débit derrière `prepareSvDebit`.

### 13.2 Calypso Card API

- **`SvAction` est supprimée**.
- `prepareSvGet(svOperation: SvOperation) → Self` — ne prend plus que l'opération.
- **`SvOperation.DEBIT` est renommée `DEBIT_UNDEBIT`**, conformément à la spécification de la carte ; `RELOAD` est inchangée.
- **Nouvelle opération** `prepareSvUndebit(amount: Int, date: ByteArray, time: ByteArray) → Self` — annule, totalement ou partiellement, un débit précédent ; montant dans `0..32768`.
- `prepareSvDebit(amount: Int, date: ByteArray, time: ByteArray) → Self` — ne fait plus que le débit ; montant dans `0..32767`.
- `prepareSvReload(amount: Int, date: ByteArray, time: ByteArray, free: ByteArray) → Self` — montant dans `-8388608..8388607` (un rechargement négatif s'exprime directement par un montant négatif).
- **Les surcharges sans données** `prepareSvDebit(int)` et `prepareSvReload(int)` sont supprimées : tous les paramètres sont **obligatoires et non nullables** ; les champs `date`, `time` et `free` (2 octets chacun) sont enregistrés dans le journal SV.
- Chaque commande exige un *SV Get* préalable préparé avec l'opération correspondante (`DEBIT_UNDEBIT` pour le débit et l'annulation, `RELOAD` pour le rechargement).

### 13.3 Justification

Le modèle suit désormais exactement la spécification de la carte : trois commandes, deux contextes de *SV Get*. La plage de `amount` et les données du journal sont explicites dans chaque opération.

---

## 14. Thème 13 — Tolérance du fichier ou de l'enregistrement absent en session sécurisée

### 14.1 Motivation

Toutes les cartes Calypso tolèrent désormais les status words `6A82h` (*File Not Found*) et `6A83h` (*Record Not Found*) en session sécurisée pour les commandes de lecture (*Select File*, *Get Data*, *Read Binary*, *Read Records*, *Read Record Multiple*, *Search Record Multiple*). Sur un parc hétérogène, la présence d'un fichier ou d'un enregistrement n'est pas toujours connue à l'avance, et une lecture infructueuse ne devrait pas annuler la session.

La tolérance est **explicitement activée par l'intégrateur** : le comportement par défaut, plus strict, reste l'échec de la transaction en session.

### 14.2 Calypso Card API

- **Deux nouveaux réglages de sécurité** : `authorizeFileNotFoundError() → Self` et `authorizeRecordNotFoundError() → Self`, portés par l'interface parente `SecuritySettings` et donc disponibles dans `SymmetricCryptoSecuritySettings` comme dans `AsymmetricCryptoSecuritySettings`. Ils autorisent la carte à répondre `6A82h` ou `6A83h` **en session** sans faire échouer la transaction : la commande concernée n'est simplement pas appliquée à la `CalypsoCard` et la session se poursuit. Par défaut, ils sont désactivés.
- **Lectures** (`prepareReadBinary`, `prepareReadCounter`, `prepareReadRecords`) et **sélection de fichier** (`prepareSelectFileByLid`, `prepareSelectFileByControl`) : **hors session**, un fichier absent n'a jamais fait échouer le traitement, et ce mode *best-effort* est inchangé ; **en session**, le traitement échoue sauf si le réglage correspondant a été activé. L'offset invalide conserve les deux modes *best-effort* (hors session) et *strict* (en session).
- **L'erreur `SelectFileException` est supprimée**.
- Les restrictions d'usage en session de `prepareGetData`, `prepareReadRecord`, `prepareReadRecordsPartially` et `prepareSearchRecords` sont **inchangées**.

---

## 15. Thème 14 — Extensions crypto et entrelacement des commandes

### 15.1 Motivation

Pendant une transaction carte, l'application doit pouvoir accéder aux opérations spécifiques du module crypto (par exemple le calcul de signature par un SAM) afin d'**entrelacer** commandes carte et commandes crypto dans une même transaction. L'extension crypto est l'instance créée, pour la transaction courante, par la factory du module crypto attachée aux réglages de sécurité.

### 15.2 Calypso Card API

- `<E extends CardTransactionCryptoExtension> E getCryptoExtension(Class<E> cryptoExtensionClass)` devient **`getCryptoExtension() → CardTransactionCryptoExtension`** :
  - l'opération renvoie **l'instance créée pour cette transaction** ; des appels successifs renvoient la même instance ;
  - les commandes préparées via l'extension rejoignent la **même file** que les commandes carte, dans l'ordre des appels, et sont traitées par le même `processCommands()` ;
  - l'appelant convertit l'instance vers le type concret défini par le module crypto utilisé (par exemple `CardTransactionLegacySamExtension`).

### 15.3 Legacy SAM API

- `CardTransactionLegacySamExtension.prepareComputeSignature(commandId, data)` et `prepareVerifySignature(commandId, data)` suivent le modèle `commandId` (cf. §8.5).

### 15.4 Justification

Le paramètre `Class<E>` n'existait que pour contourner l'effacement des types de la JVM ; il n'a pas d'équivalent dans d'autres langages (Rust notamment). La signature retenue est **identique dans tous les bindings**, et la clause d'identité garantit que l'extension obtenue est bien celle qui partage la file de commandes de la transaction.

---

## 16. Clarifications normatives

Les spécifications apportent en outre des clarifications qui ne changent pas les signatures mais précisent le contrat :

- **Card API — règles de construction des APDU** : les commandes doivent être strictement conformes à ISO/IEC 7816-3 ; une commande de cas 4 doit inclure le champ `Le`, dont la valeur `00h` est **recommandée** (elle était auparavant présentée comme obligatoire).
- **Card API — limitations** : la transmission des commandes *Select Application* par nom de DF (réservée à la `CardSelectionRequest`) et *Get Response* (les status words `61XYh` et `6CXYh` sont traités automatiquement par l'implémentation du lecteur) ne peut pas être demandée.
- **Card API — contre-mesures temporelles** : le mécanisme de contrôle du temps d'exécution des échanges APDU est explicitement présenté comme solution de contre-mesure face au relai et à l'émulation (cf. Thème 2).
- **Reader API — cycle de vie des `SmartCard`** : désormais normatif (cf. §2.3.2).
- **Toutes les APIs — natures des pré-conditions** : un critère explicite distingue *Range* (position dans une collection ou une image mémoire exposée par l'API) et *Argument* (toute autre valeur invalide, y compris les valeurs bornées par le protocole de la carte ou du SAM).
- **Storage Card API — périmètre** : la section *Scope* liste explicitement les produits supportés (MIFARE Ultralight, MIFARE Classic 1K, MIFARE Classic 4K, ST25 SRT512), identifiés par les valeurs de `StorageCardProductType`.

---

## 17. Éléments en cours d'étude

Les éléments suivants figurent en gris dans le diagramme de la Legacy SAM API ; ils ne font **pas** partie du périmètre normatif soumis à validation :

- `LegacySamApiFactory.createSecureReadTransactionManager(samReader, sam, securitySettings)` et l'interface `SecureReadTransactionManager` ;
- `FreeTransactionManager.preparePlainLoadWorkKey(...)` et `preparePlainExportWorkKey(...)` ;
- `LegacySamSelectionExtension.prepareReadCaadRecord(...)` / `prepareReadCaadRecords(...)` et leurs équivalents sur `ReadTransactionManager` ;
- `SecureWriteTransactionManager.prepareWriteCaadRecord(...)`.

---

## 18. Procédure de migration

La migration du code applicatif depuis les versions en production vers les nouvelles versions fera l'objet d'un **guide technique de migration dédié**, publié séparément après validation par le TC Terminal et après alignement des implémentations Java Keypop associées.

Ce guide aura pour objectif de **simplifier autant que possible la transition** : correspondance 1:1 des éléments retirés / renommés / refondus (dont l'annexe A constitue la base), patrons de réécriture (`avant` / `après`) pour les cas d'usage les plus fréquents, règles d'adoption progressive et pièges connus. Il décrira également la **réalisation Java** des principes de notation du Thème 10 (classes de données, `Self`, valeurs par défaut, nullabilité).

---

## 19. Suite et validation par le TC Terminal

### 19.1 Périmètre soumis à validation

Le présent document soumet à la validation du **TC Terminal de la CNA** :

1. **Le principe** des quatorze thèmes d'évolution (§2 à §15) et la cohérence d'ensemble du chantier (versions 3.0.0 pour Reader / Card / Calypso Card, 1.0.0 pour Definitions, 2.0.0 pour Legacy SAM / Generic Card / Storage Card, 0.2.0 pour Crypto Symmetric, 0.3.0 pour Crypto Asymmetric).
2. **Les choix de conception** documentés dans les sections « Justification », en particulier :
   - le modèle multicanal explicite reposant sur la `SmartCard(Spi)` comme cible nommée et la hiérarchie à trois niveaux des gestionnaires de transactions (§2) ;
   - le bornage de durée au niveau APDU, session Calypso, commande générique et lecture de carte de stockage, avec réglages par CSN et par FCI (§3) ;
   - la fusion du patron Observateur en une seule SPI `CardReaderEventHandler` (§4) ;
   - l'énumération `SecureSessionState` (§5) ;
   - l'extraction de `RfTechnology` et `CardType` dans la Terminal Reader Definitions API et les paramètres de détection `CardDetectionSettings` (§7) ;
   - le modèle `commandId` généralisé (§8) ;
   - la découverte normalisée des lecteurs par `CardReaderProvider` (§9) ;
   - la **hiérarchie de gestionnaires de sélection par mode** et les trois types de résultat (§10) ;
   - la **notation indépendante du langage** et ses principes, qui visent à élargir le choix des langages d'implémentation (KMP, Rust, Swift, etc.) (§11) ;
   - les **données sans calcul** et l'accès aux données brutes (§12) ;
   - le modèle SV aligné sur la spécification de la carte (§13) ;
   - la tolérance du fichier absent en session (§14) ;
   - l'accès à l'extension crypto avec clause d'identité (§15).
3. **Le contenu détaillé des neuf spécifications** et de leurs diagrammes (cf. [Documents de référence](#documents-de-référence)).
4. **L'introduction** de la nouvelle API socle Reader Definitions.
5. **Le principe** d'une procédure de migration dédiée (cf. §18).

### 19.2 Points d'attention pour la revue

- la **stabilité du contenu initial** des énumérations `RfTechnology` et `CardType` (§7.2), en particulier la représentation d'ISO 14443-4 par une seule valeur `ISO_14443_4` ;
- la **suppression intégrale** de `ConfigurableCardReader` sans phase de dépréciation (§7.3) ;
- la **règle de résolution des bornes de durée** Calypso : priorité des réglages par CSN sur les réglages par FCI, `Long.MAX_VALUE` comme valeur de renvoi, sous-ensemble portable d'expressions régulières (§3.3) ;
- l'**application des bornes de durée Calypso** (§3.1, §3.3) : ce qui est mesuré (le seul échange de la commande concernée) et la conséquence d'un dépassement (annulation automatique de la session ouverte, remontée de l'erreur hors session) sont désormais spécifiés ;
- la **gradation à trois niveaux** des gestionnaires de transactions (§2.2) ;
- le **remplacement des surcharges** par des noms d'opérations uniques (§11.2), qui modifie de nombreux noms d'opérations pour les intégrateurs Java ;
- le **remplacement des interfaces « builder »** par des classes de données (§11.2, §12), dont la réalisation Java reste à définir ;
- le **passage de `SvOperation.DEBIT` à `DEBIT_UNDEBIT`** et la suppression des surcharges SV sans données (§13).

### 19.3 Étapes suivantes

Une fois les versions validées par le TC Terminal :

1. **Finalisation des spécifications** : passage des dépôts de leurs versions `…-SNAPSHOT` à leurs versions finales ; retrait ou spécification des éléments en cours d'étude (§17).
2. **Création du nouveau module Java** `keypop-reader-definitions-jvm-api`, et **alignement des modules Java Keypop** existants (`keypop-reader-java-api`, `keypop-card-java-api`, `keypop-calypso-card-java-api`, `keypop-calypso-crypto-legacysam-java-api`, `keypop-calypso-crypto-symmetric-java-api`, `keypop-calypso-crypto-asymmetric-java-api`, `keypop-genericcard-jvm-api`, `keypop-storagecard-java-api`) sur leurs nouvelles versions.
3. **Rédaction et publication du guide technique de migration** (cf. §18).
4. **Communication** de la disponibilité des nouvelles versions aux intégrateurs et aux groupes de travail CNA concernés.

---

## Annexe A — Correspondance détaillée par API

Cette annexe liste, pour chaque API, le devenir de chaque élément des versions Java en production. Les éléments **inchangés** (hors passage à la notation du Thème 10 : types de base, `Self`, nullabilité explicite) ne sont pas repris. Les signatures « après » sont exprimées dans la notation des spécifications.

### A.1 Terminal Reader API (Java 2.1.0 → 3.0.0)

| Élément en production | Devenir |
|---|---|
| `ReaderApiFactory.createCardSelectionManager()` | Supprimée → `createSingleCardSelectionManager()`, `createMultipleCardSelectionManager()`, `createMultichannelCardSelectionManager()` |
| `ReaderApiFactory.createBasicCardSelector()`, `createIsoCardSelector()` | Supprimées (sélecteurs = classes de données) |
| — | Ajoutée : `ReaderApiFactory.getCardReaderProvider() → CardReaderProvider` ; interface `CardReaderProvider` |
| `ConfigurableCardReader` (`activateProtocol`, `deactivateProtocol`, `getCurrentProtocol`) | Supprimée |
| `ObservableCardReader.setReaderObservationExceptionHandler`, `addObserver`, `removeObserver`, `clearObservers`, `countObservers` | Supprimées |
| `ObservableCardReader.startCardDetection(DetectionMode)` | → `startCardDetection(settings: CardDetectionSettings, eventHandler: CardReaderEventHandler)` |
| `ObservableCardReader.finalizeCardProcessing()` | → `endCardProcessing()` |
| — | Ajoutée : `ObservableCardReader.clearScheduledCardSelectionScenario()` |
| `ObservableCardReader.DetectionMode` (`REPEATING`, `SINGLESHOT`) | → `DetectionMode` (`REPEATING`, `SINGLE_SHOT`) |
| `ObservableCardReader.NotificationMode` | → `CardPresenceNotificationPolicy` (namespace `reader.selection`) |
| `CardReaderEvent` (interface) | → classe de données `CardReaderEvent` (`readerName`, `type`, `scheduledCardSelectionsResponse?`) |
| `CardReaderEvent.Type` (`…`, `UNAVAILABLE`) | → `CardReaderEventType` (`…`, `READER_UNREGISTERED`) |
| `ChannelControl` | Supprimée |
| `CardCommunicationException`, `ReaderCommunicationException`, `InvalidCardResponseException` | → `CardCommunication`, `ReaderCommunication`, `InvalidCardResponse` |
| `ReaderProtocolNotSupportedException` | Supprimée |
| `reader.selection.InvalidCardResponseException` | Supprimée (doublon) |
| `CardSelectionManager.setMultipleSelectionMode()`, `prepareReleaseChannel()` | Supprimées |
| `CardSelectionManager.prepareSelection(CardSelector<?>, CardSelectionExtension) → int` | → `prepareSelection(selectionId: Int, cardSelector: CardSelector, cardSelectionExtension: CardSelectionExtension) → Self` |
| `CardSelectionManager.importCardSelectionScenario(String) → int` | → `importCardSelectionScenario(cardSelectionScenario: String) → Self` (remplace le scénario) |
| `CardSelectionManager.processCardSelectionScenario(CardReader)` | → `processCardSelectionScenario(reader)` sur `SingleCardSelectionManager` / `MultipleCardSelectionManager` ; `processCardSelectionScenario(reader, channelSelectionPolicy)` sur `MultichannelCardSelectionManager` |
| `CardSelectionManager.scheduleCardSelectionScenario(ObservableCardReader, NotificationMode)` | → `scheduleCardSelectionScenario(observableCardReader, cardPresenceNotificationPolicy)` sur les gestionnaires mono-canal |
| `CardSelectionManager.parseScheduledCardSelectionsResponse(...)` | → sur les gestionnaires mono-canal, renvoie le résultat typé |
| `CardSelectionManager.importProcessedCardSelectionScenario(String)` | → sur chaque gestionnaire, renvoie le résultat typé |
| `CardSelectionResult` (`getSmartCards`, `getActiveSmartCard`, `getActiveSelectionIndex`) | Supprimée → `SingleCardSelectionResult`, `MultipleCardSelectionResult`, `MultichannelCardSelectionResult` |
| — | Ajoutée : `ChannelSelectionPolicy` |
| `CardSelector<T>` (`filterByCardProtocol`, `filterByPowerOnData`) | → interface scellée `CardSelector` ; `filterByCardProtocol` supprimée ; `filterByPowerOnData` → propriété `powerOnDataRegex` ; ajout de la propriété `cardType` |
| `BasicCardSelector` (interface) | → classe de données (`cardType?`, `powerOnDataRegex?`) |
| `CommonIsoCardSelector<T>` (`filterByDfName(byte[])`, `filterByDfName(String)`, `setFileOccurrence`, `setFileControlInformation`) | Supprimée → propriétés `dfName: ByteArray?`, `fileOccurrence`, `fileControlInformation` d'`IsoCardSelector` ; `filterByDfName(String)` supprimée |
| `CommonIsoCardSelector.FileOccurrence`, `.FileControlInformation` | → `FileOccurrence`, `FileControlInformation` |
| `IsoCardSelector` (interface) | → classe de données |
| `SmartCard.getPowerOnData()` | → `getPowerOnData() → String?` ; ajout de `isActive() → Boolean` |
| `IsoSmartCard.getSelectApplicationResponse()` | → `getSelectApplicationResponse() → ByteArray?` ; ajout de `isBasicChannel() → Boolean` |
| `CardReaderObserverSpi.onReaderEvent(CardReaderEvent)` | → `CardReaderEventHandler.onReaderEvent(cardReaderEvent: CardReaderEvent)` |
| `CardReaderObservationExceptionHandlerSpi.onReaderObservationError(String contextInfo, String readerName, Throwable e)` | → `CardReaderEventHandler.onReaderError(context: String, readerName: String, error: Any)` |
| `CardTransactionManager<T>.processCommands(ChannelControl) → T` | → `CardTransactionManager.processCommands() → Unit` (non générique) |
| — | Ajoutées : `IsoCardTransactionManager`, `MultichannelCardTransactionManager` |
| — | Ajoutée : classe de données `CardDetectionSettings` |

### A.2 Terminal Card API (Java 2.0.1 → 3.0.0)

| Élément en production | Devenir |
|---|---|
| `ApduRequestSpi` (`getApdu`, `getSuccessfulStatusWords`, `getInfo`) | → classe de données `ApduRequest` (`apdu`, `successfulStatusWords = setOf(0x9000)`, `info: String? = null`) + `apduExchangeMaxDuration: Long? = null` |
| `ApduResponseApi` (`getApdu`, `getDataOut`, `getStatusWord`, `Serializable`) | → classe de données `ApduResponse` (`apdu`) + `apduExchangeDuration: Long?` ; `getDataOut`, `getStatusWord` supprimées |
| `CardRequestSpi` (`getApduRequests`, `stopOnUnsuccessfulStatusWord`) | → classe de données `CardRequest` |
| `CardResponseApi` (`getApduResponses`, `isLogicalChannelOpen`) | → classe de données `CardResponse` (`apduResponses`) ; `isLogicalChannelOpen` supprimée |
| `CardSelectionRequestSpi` (`getSuccessfulSelectionStatusWords`, `getCardRequest`) | → classe de données `CardSelectionRequest` (`successfulSelectionStatusWords = setOf(0x9000)`, `cardRequest: CardRequest? = null`) |
| `CardSelectionResponseApi` (`getPowerOnData`, `getSelectApplicationResponse`, `hasMatched`, `getCardResponse`) | → classe de données `CardSelectionResponse` (mêmes informations, nullabilité explicite) + `channel: Int` |
| `CardSelectionExtensionSpi.getCardSelectionRequest()`, `parse(CardSelectionResponseApi)` | → types `CardSelectionRequest` / `CardSelectionResponse` |
| `SmartCardSpi` | Ajout de `deactivate() → Unit` |
| — | Ajoutée : `MultichannelSmartCardSpi` (`getChannel() → Int`) |
| `ProxyReaderApi.transmitCardRequest(CardRequestSpi, ChannelControl)`, `releaseChannel()` | Supprimées → `transmitCardRequest(cardRequest, smartCard)`, `transmitCardRequestAndCloseChannel(cardRequest, multichannelSmartCard)`, `closeChannel(multichannelSmartCard)` |
| `ChannelControl` | Supprimée |
| `AbstractApduException` (`getCardResponse`, `isCardResponseComplete`) | Supprimée ; propriétés `cardResponse: CardResponse?` et `isCardResponseComplete: Boolean` portées par les erreurs APDU |
| `CardBrokenCommunicationException`, `ReaderBrokenCommunicationException`, `UnexpectedStatusWordException`, `ParseException` | → `CardBrokenCommunication` (levée aussi si la carte n'est plus active), `ReaderBrokenCommunication`, `UnexpectedStatusWord`, `Parse` |
| — | Ajoutée : erreur `ApduExchangeDurationExceeded` |

### A.3 Terminal Calypso Card API (Java 2.2.0 → 3.0.0)

| Élément en production | Devenir |
|---|---|
| `CalypsoCardApiFactory.createSearchCommandData()` | Supprimée |
| `CalypsoCard.getProductType() → CalypsoCard.ProductType` | → `getProductType() → CalypsoCardProductType` |
| `CalypsoCard.getDirectoryHeader()`, `getFileBySfi`, `getFileByLid`, `getSvLoadLogRecord`, `getSvDebitLogLastRecord` | → retours nullables explicites |
| — | Ajoutées : `CalypsoCard.getCounterValuesBySfi`, `getCounterValuesByLid`, `getMatchingRecordNumbers(commandId)`, `getSvLoadLogRecordRawData`, `getSvDebitLogLastRecordRawData`, `getSvDebitLogAllRecordsRawData` |
| `CalypsoCard.ProductType` | → `CalypsoCardProductType` |
| `TransactionManager.prepareDecreaseCounters(sfi, counterNumberToDecValueMap)`, `prepareIncreaseCounters(sfi, counterNumberToIncValueMap)` | → paramètres renommés `decrementValues`, `incrementValues` |
| `SymmetricCryptoSecuritySetting`, `AsymmetricCryptoSecuritySetting` | → `SymmetricCryptoSecuritySettings`, `AsymmetricCryptoSecuritySettings` |
| `CalypsoCardApiFactory.createSymmetricCryptoSecuritySetting(...)`, `createAsymmetricCryptoSecuritySetting(...)` | → `createSymmetricCryptoSecuritySettings(...)`, `createAsymmetricCryptoSecuritySettings(...)` ; paramètre `securitySetting` → `securitySettings` dans `createSecure…TransactionManager` |
| `CalypsoCardSelectionExtension.prepareSelectFile(short)` / `prepareSelectFile(SelectFileControl selectControl)` | → `prepareSelectFileByLid(lid)` / `prepareSelectFileByControl(selectFileControl)` |
| `DirectoryHeader` (interface ; `getKif(level)`, `getKvc(level)`) | → classe de données ; `kifByAccessLevel`, `kvcByAccessLevel` : `Map<WriteAccessLevel, Byte>` |
| `ElementaryFile` (interface ; `getData()`) | → classe de données (`sfi`, `header?`, `records: SortedMap<Int, ByteArray>`) |
| `ElementaryFile.Type` | → `ElementaryFileType` |
| `FileData` (toutes opérations) | Supprimée (cf. §12.2) |
| `FileHeader` (interface) | → classe de données (`efType: ElementaryFileType`) |
| `SvDebitLogRecord`, `SvLoadLogRecord` (interfaces, `getRawData`) | → classes de données sans `rawData` |
| `SearchCommandData.setSfi`, `startAtRecord`, `setOffset`, `enableRepeatedOffset`, `setSearchData`, `setMask`, `fetchFirstMatchingResult` | → propriétés de la classe de données `SearchCommandData` : `sfi`, `startAtRecord = 1`, `offset = 0`, `repeatedOffset = false`, `searchData`, `mask: ByteArray? = null`, `fetchFirstMatchingResult = false` |
| `SearchCommandData.getMatchingRecordNumbers()` | → `CalypsoCard.getMatchingRecordNumbers(commandId: Int) → List<Int>?` |
| `TransactionManager<T>` et sous-interfaces génériques | → non génériques, retours `Self` ; `TransactionManager` étend `IsoCardTransactionManager` |
| `TransactionManager.prepareSelectFile(short)` / `(SelectFileControl)` | → `prepareSelectFileByLid` / `prepareSelectFileByControl` |
| `TransactionManager.prepareSearchRecords(SearchCommandData)` | → `prepareSearchRecords(commandId: Int, data: SearchCommandData)` |
| `TransactionManager.processCommands(ChannelControl)` | Supprimée (héritage de `CardTransactionManager.processCommands()`) |
| — | Ajoutée : `TransactionManager.getSecureSessionState() → SecureSessionState` ; énumération `SecureSessionState` |
| `SecureTransactionManager.getCryptoExtension(Class<E>) → E` | → `getCryptoExtension() → CardTransactionCryptoExtension` |
| `SecureSymmetricCryptoTransactionManager.prepareSvGet(SvOperation, SvAction)` | → `prepareSvGet(svOperation: SvOperation)` |
| `prepareSvReload(int)`, `prepareSvDebit(int)` | Supprimées |
| — | Ajoutée : `prepareSvUndebit(amount, date, time)` |
| `SvAction` | Supprimée |
| `SvOperation.DEBIT` | → `SvOperation.DEBIT_UNDEBIT` |
| — | Ajoutée : interface `SecuritySettings`, parente de `SymmetricCryptoSecuritySettings` et `AsymmetricCryptoSecuritySettings`, portant `assignOpenSecureSessionMaxDurationByCsn/ByFci(...)`, `assignCloseSecureSessionMaxDurationByCsn/ByFci(...)`, `authorizeFileNotFoundError()` et `authorizeRecordNotFoundError()` |
| — | Ajoutées : `SymmetricCryptoSecuritySettings.assignSvCommandMaxDurationByCsn/ByFci(...)` ; les bornes de session sont héritées de `SecuritySettings` |
| `ChannelControl` | Supprimée |
| `CardIOException`, `ReaderIOException`, `UnexpectedCommandStatusException`, `SelectFileException` | Supprimées |
| `CardSignatureNotVerifiableException`, `CryptoException`, `CryptoIOException`, `InconsistentDataException`, `InvalidCardSignatureException`, `InvalidCertificateException`, `InvalidPinException`, `SessionBufferOverflowException`, `UnauthorizedKeyException` | → mêmes noms sans suffixe `Exception` |

### A.4 Terminal Calypso Crypto Legacy SAM API (Java 1.0.0 → 2.0.0)

| Élément en production | Devenir |
|---|---|
| `LegacySamApiFactory.createSecuritySetting()`, `createKeyPairContainer()`, `createLegacyCardCertificateComputationData()`, `createBasicSignatureComputationData()`, `createTraceableSignatureComputationData()`, `createBasicSignatureVerificationData()`, `createTraceableSignatureVerificationData()` | Supprimées (classes de données) |
| `LegacySam.getProductType() → LegacySam.ProductType` | → `getProductType() → LegacySamProductType` |
| `LegacySam.getCounter(int)`, `getCounterCeiling(int)` | Supprimées (utiliser `getCounters()`, `getCounterCeilings()`) |
| `LegacySam.getCounterIncrementAccess(int)` | → `getCounterIncrementAccesses() → SortedMap<Int, CounterIncrementAccess>` |
| `LegacySam.getSamParameters() → SamParameters` | → `getSamParameters() → ByteArray?` ; `SamParameters` supprimée |
| `LegacySam.getSystemKeyParameter(SystemKeyType)` | → `getSystemKeyParameters(systemKeyType) → KeyParameters?` |
| `LegacySam.getWorkKeyParameter(int)` / `(byte, byte)` | → `getWorkKeyParametersByRecordNumber` / `getWorkKeyParametersByKifKvc` |
| — | Ajoutées : `LegacySam.getSystemKeyParametersRawData`, `getWorkKeyParametersRawDataByRecordNumber`, `getWorkKeyParametersRawDataByKifKvc`, `getKeyPair(commandId)`, `getComputedCardCertificate(commandId)`, `getSignature(commandId)`, `getSignedData(commandId)`, `isSignatureValid(commandId)` |
| `LegacySam.ProductType` | → `LegacySamProductType` |
| `KeyParameter` (interface ; `getRawData`, `getParameterValue(int)`) | → classe de données `KeyParameters` (`kif`, `kvc`, `algorithm`, `parameterValues`) |
| `LegacySamSelectionExtension.setUnlockData(String, ProductType)` | → `setUnlockDataForProductType(unlockData, productType)` |
| `LegacySamSelectionExtension.setStaticUnlockDataProvider(provider)` / `setDynamicUnlockDataProvider(provider)` | → `setStaticUnlockDataProviderWithDeferredReader(provider)` / `setDynamicUnlockDataProviderWithDeferredReader(provider)` |
| `LegacySamSelectionExtension.prepareReadWorkKeyParameters(int)` / `(byte, byte)` | → `prepareReadWorkKeyParametersByRecordNumber` / `prepareReadWorkKeyParametersByKifKvc` |
| `LegacySamRevocationServiceSpi.isSamRevoked(byte[])` | Supprimée (subsiste `isSamRevoked(serialNumber, counterValue: Int)`) |
| `TransactionManager<T>` et sous-interfaces génériques | → non génériques, retours `Self` |
| `TransactionManager.processCommands()`, `processCommands(ChannelControl)` | Supprimées (héritage de `CardTransactionManager.processCommands()`) |
| `ReadTransactionManager.prepareReadWorkKeyParameters(int)` / `(byte, byte)` | → `…ByRecordNumber` / `…ByKifKvc` |
| `FreeTransactionManager.prepareGenerateCardAsymmetricKeyPair(KeyPairContainer)` | → `prepareGenerateCardAsymmetricKeyPair(commandId: Int)` |
| `FreeTransactionManager.prepareComputeCardCertificate(data)` | → `prepareComputeCardCertificate(commandId: Int, data)` |
| `FreeTransactionManager` / `CardTransactionLegacySamExtension` `.prepareComputeSignature(data)`, `.prepareVerifySignature(data)` | → `prepareComputeSignature(commandId: Int, data)`, `prepareVerifySignature(commandId: Int, data)` |
| `SecureWriteTransactionManager.prepareTransferWorkKeyDiversified(…, diversifier)` | → `prepareTransferWorkKeyDiversifiedWithSpecificDiversifier(…, diversifier)` |
| `KeyPairContainer` | Supprimée |
| `LegacyCardCertificateComputationData.setCardPublicKey`, `setStartDate`, `setEndDate`, `setCardAid`, `setCardSerialNumber`, `setCardStartupInfo` | → propriétés de la classe de données : `cardPublicKey`, `startDate`, `endDate`, `cardAid`, `cardSerialNumber`, `cardStartupInfo` |
| `LegacyCardCertificateComputationData.getCertificate()` | → `LegacySam.getComputedCardCertificate(commandId: Int) → ByteArray?` |
| `SignatureComputationData<T>` | → interface scellée `SignatureComputationData` |
| `SignatureComputationData.setData(byte[] data, byte kif, byte kvc)`, `setSignatureSize(int)`, `setKeyDiversifier(byte[])` | → propriétés `data`, `kif`, `kvc`, `signatureSize = 8`, `keyDiversifier: ByteArray? = null` des classes de données |
| `SignatureComputationData.getSignature()` | → `LegacySam.getSignature(commandId: Int) → ByteArray?` |
| `BasicSignatureComputationData`, `TraceableSignatureComputationData` | → classes de données implémentant `SignatureComputationData` |
| `TraceableSignatureComputationData.withSamTraceabilityMode(int offset, SamTraceabilityMode mode)`, `withoutBusyMode()` | → propriétés `traceabilityOffset = 0`, `samTraceabilityMode: SamTraceabilityMode? = null`, `busyMode = true` |
| `TraceableSignatureComputationData.getSignedData()` | → `LegacySam.getSignedData(commandId: Int) → ByteArray?` |
| `SignatureVerificationData<T>` | → interface scellée `SignatureVerificationData` |
| `SignatureVerificationData.setData(byte[] data, byte[] signature, byte kif, byte kvc)`, `setKeyDiversifier(byte[])` | → propriétés `data`, `signature`, `kif`, `kvc`, `keyDiversifier: ByteArray? = null` des classes de données |
| `SignatureVerificationData.isSignatureValid()` | → `LegacySam.isSignatureValid(commandId: Int) → Boolean?` |
| `BasicSignatureVerificationData`, `TraceableSignatureVerificationData` | → classes de données implémentant `SignatureVerificationData` |
| `TraceableSignatureVerificationData.withSamTraceabilityMode(int offset, SamTraceabilityMode mode, LegacySamRevocationServiceSpi service)`, `withoutBusyMode()` | → propriétés `traceabilityOffset = 0`, `samTraceabilityMode: SamTraceabilityMode? = null`, `samRevocationService: LegacySamRevocationServiceSpi? = null`, `busyMode = true` |
| `SecuritySetting.setControlSamResource(samReader, controlSam)` | → classe de données `SecuritySettings` (`samReader`, `controlSam`) ; paramètre `securitySetting` → `securitySettings` dans `createSecureWriteTransactionManager` et `createAsyncTransactionCreatorManager` |
| `ReaderIOException`, `SamIOException`, `UnexpectedCommandStatusException` | Supprimées |
| `InconsistentDataException`, `InvalidSignatureException`, `SamRevokedException` | → `InconsistentData`, `InvalidSignature`, `SamRevoked` |

### A.5 Terminal Calypso Crypto Symmetric API (Java 0.1.1 → 0.2.0)

| Élément en production | Devenir |
|---|---|
| `SvCommandSecurityDataApi.getSvGetRequest()`, `getSvGetResponse()`, `getSvCommandPartialRequest()` (entrées) | → paramètres `svGetRequest`, `svGetResponse`, `svCommandPartialRequest` de `computeSvCommandSecurityData` |
| `SvCommandSecurityDataApi.setSerialNumber`, `setTransactionNumber`, `setTerminalChallenge`, `setTerminalSvMac` (sorties) | → propriétés `serialNumber`, `transactionNumber`, `terminalChallenge`, `terminalSvMac` de la classe de données `SvCommandSecurityData` (namespace `calypso.crypto.symmetric.spi`) |
| `SymmetricCryptoCardTransactionManagerSpi.computeSvCommandSecurityData(SvCommandSecurityDataApi) → void` | → `computeSvCommandSecurityData(svGetRequest, svGetResponse, svCommandPartialRequest) → SvCommandSecurityData` |
| `SymmetricCryptoCardTransactionManagerSpi.cipherPinForPresentation(…, Byte kif, Byte kvc)`, `cipherPinForModification(…, Byte kif, Byte kvc)` | → `kif: Byte`, `kvc: Byte` non nullables |
| `SymmetricCryptoCardTransactionManagerFactorySpi.createCardTransactionManager(…, List<byte[]> transactionAuditData)` | → `transactionAuditData: MutableList<ByteArray>` |
| `SymmetricCryptoException`, `SymmetricCryptoIOException` | → `SymmetricCrypto`, `SymmetricCryptoIO` |

### A.6 Terminal Calypso Crypto Asymmetric API (Java 0.2.0 → 0.3.0)

| Élément en production | Devenir |
|---|---|
| `CaCertificateContentSpi.getPublicKey`, `getPublicKeyReference`, `getStartDate`, `getEndDate`, `isAidTruncated`, `getAid`, `isCaCertificatesAuthenticationAllowed`, `isCardCertificatesAuthenticationAllowed` | → propriétés de la classe de données `CaCertificateContent` : `publicKey`, `publicKeyReference`, `startDate`, `endDate`, `isAidTruncated`, `aid: ByteArray?`, `isCaCertificatesAuthenticationAllowed`, `isCardCertificatesAuthenticationAllowed` |
| `CaCertificateContentSpi.isAidCheckRequested()` | Supprimée (déductible de `aid`, `null` si la vérification n'est pas demandée) |
| `CaCertificateSpi.checkCertificateAndGetContent(CaCertificateContentSpi) → CaCertificateContentSpi` | → `checkCertificateAndGetContent(issuerCertificateContent: CaCertificateContent) → CaCertificateContent` |
| `PcaCertificateSpi.checkCertificateAndGetContent() → CaCertificateContentSpi` | → `… → CaCertificateContent` |
| `CardCertificateSpi.checkCertificateAndGetPublicKey(CaCertificateContentSpi) → CardPublicKeySpi` | → `checkCertificateAndGetPublicKey(issuerCertificateContent: CaCertificateContent) → ByteArray` |
| `CardPublicKeySpi` (`getRawValue`) | Supprimée |
| `AsymmetricCryptoCardTransactionManagerSpi.initTerminalPkiSession(CardPublicKeySpi)` | → `initTerminalPkiSession(cardPublicKey: ByteArray)` |
| `AsymmetricCryptoException`, `CertificateValidationException` | → `AsymmetricCrypto`, `CertificateValidation` |

### A.7 Terminal Generic Card API (Java 1.0.0 → 2.0.0)

| Élément en production | Devenir |
|---|---|
| `CardTransactionManager` (extends `CardTransactionManager<…>` de la Reader API) | → `GenericCardTransactionManager` (étend `IsoCardTransactionManager`) |
| `prepareApdu(String)` | Supprimée |
| `prepareApdu(byte[])` | → `prepareCommand(apdu: ByteArray)` |
| `prepareApdu(byte cla, byte ins, byte p1, byte p2, byte[] dataIn, Byte le)` | Supprimée |
| — | Ajoutées : `prepareCommandWithId(commandId, apdu)`, `prepareCommandWithMaxDuration(commandId, apdu, maxDuration)`, `getLastExecutionResponse(commandId) → ByteArray?` |
| `getResponsesAsByteArrays()` | → `getLastExecutionResponses() → List<ByteArray>` |
| `getResponsesAsHexStrings()` | Supprimée |
| `GenericCardApiFactory.createCardTransaction(reader, card)` | → `createGenericCardTransactionManager(reader, card)` |

### A.8 Terminal Storage Card API (Java 1.2.0 → 2.0.0)

| Élément en production | Devenir |
|---|---|
| `ProductType` (méthodes `getBlockCount`, `getBlockSize`, `hasSystemBlock`, `hasWriteAcknowledgment`, `hasAuthentication`) | → `StorageCardProductType` avec propriétés `blockCount`, `blockSize`, `hasSystemBlock`, `hasWriteAcknowledgment`, `hasAuthentication` |
| `StorageCardApiFactory.createStorageCardSelectionExtension(ProductType)` | → paramètre `productType: StorageCardProductType` |
| `StorageCard.getUID()` | → `getUid()` |
| `StorageCard.getSystemBlock()`, `getBlock(int)`, `getBlocks(int, int)` | → retours `ByteArray?` |
| `StorageCardSelectionExtension` / `StorageCardTransactionManager` `.prepareMifareClassicAuthenticate(…, byte[] key)` / `(…, int keyNumber)` | → `prepareMifareClassicAuthenticateWithKey` / `prepareMifareClassicAuthenticateWithKeyNumber` |
| `StorageCardTransactionManager` (extends `CardTransactionManager<…>`) | → non générique, retours `Self` |
| `StorageCardTransactionManager.prepareReadSystemBlock()`, `prepareWriteSystemBlock(byte[])` *(dépréciées)* | Supprimées |
| `StorageCardTransactionManager.prepareSt25WriteSystemBlock(byte[])` | → `prepareSt25WriteSystemBlock(commandId: Int, data: ByteArray)` |
| — | Ajoutées : classe de données `StorageCardSecuritySettings` (`readCommandMaxDurations`) ; paramètre `securitySettings` ajouté à `StorageCardApiFactory.createStorageCardTransactionManager(...)` |
| `StorageCardTransactionManager.prepareWriteBlocks(int, byte[])` | → `prepareWriteBlocks(commandId: Int, fromBlockAddress: Int, data: ByteArray)` |
| `StorageCardException` (`getBlockAddress`) | Supprimée ; les erreurs portent `blockAddress: Int?` et `commandId: Int?` |
| `SCAuthenticationFailedException` (extends `CardCommunicationException`) | → `SCAuthenticationFailed` (sans erreur parente) |
| `SCCardCommunicationException`, `SCInvalidCardResponseException`, `SCReaderCommunicationException` | → `SCCardCommunication`, `SCInvalidCardResponse`, `SCReaderCommunication` (parents inchangés) |

### A.9 Terminal Reader Definitions API (nouvelle, 1.0.0)

| Élément | Contenu |
|---|---|
| `ReaderDefinitionsApiProperties` | constante `VERSION` |
| `RfTechnology` | `ISO_14443_AB`, `INNOVATRON_B_PRIME`, `FELICA`, `ISO_15693` |
| `CardType` | `ISO_7816_3`, `ISO_14443_4`, `ISO_14443_3A_MIFARE_CLASSIC_1K`, `ISO_14443_3A_MIFARE_CLASSIC_4K`, `ISO_14443_3A_MIFARE_ULTRALIGHT`, `ISO_14443_3B_ST25_SRT512`, `INNOVATRON_B_PRIME`, `FELICA`, `ISO_15693`, `UNKNOWN` |

---

*Fin du document.*
