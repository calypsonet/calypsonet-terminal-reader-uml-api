# Changements apportés par la version 3.0.0 des APIs Terminaux

> **Statut du document** : version de travail soumise au **TC Terminal de la CNA pour validation**.

## Préambule

Ce document a pour objectif de **présenter les évolutions des APIs terminaux** introduites par leur version majeure **3.0.0** — pour les trois APIs existantes (`Reader API`, `Card API`, `Calypso Card API`) ainsi que pour la nouvelle **Terminal Definitions API** créée à cette occasion — **en vue d'une validation par les membres du TC Terminal** (Technical Committee Terminal) de la **Calypso Networks Association** (CNA).

Il décrit, pour chaque thème d'évolution :

- la **motivation** (le « pourquoi ») ;
- le **détail des changements** dans chacune des quatre APIs concernées ;
- la **justification de conception** (le « pourquoi de ce choix-là plutôt qu'un autre »).

L'alignement des implémentations Java Keypop sur la 3.0.0 et la rédaction d'un **guide technique de migration** à destination des intégrateurs interviendront ultérieurement, après validation de la 3.0.0 par le TC Terminal (cf. §8).

> **Visibilité des APIs vis-à-vis des audiences**
>
> - La **Reader API**, la **Calypso Card API** et la nouvelle **Terminal Definitions API** sont des APIs **publiques**, manipulées directement par l'**intégrateur** (le code applicatif).
> - La **Card API** est une API **interne** : elle sert de contrat d'intégration entre les implémentations de lecteurs et les extensions de cartes. **L'intégrateur n'y a pas accès**.
>
> Le présent document décrit les évolutions des quatre APIs car elles sont solidaires sur le plan de la conception (en particulier pour les thèmes 1, 2 et 6). La **Card API étant interne**, ses évolutions (cf. §2.3, §3.2, §6.2) ne nécessitent **aucune action** de la part de l'intégrateur ; elles sont absorbées par les implémentations Keypop.
>
> **Nouvelle API : Terminal Definitions API**
>
> La version 3.0.0 introduit une nouvelle API **socle** dédiée à l'hébergement des **types énumérés transverses** aux APIs Terminaux. Elle est créée à l'occasion du Thème 6 pour accueillir `RfTechnology` et `CardType`, mais sa vocation est plus large : elle est **destinée à recevoir potentiellement d'autres énumérations** qui constituent des **constantes globales** partagées par plusieurs APIs Terminaux. La Terminal Reader API **dépend** désormais de cette nouvelle API ; les autres APIs Terminaux pourront en dépendre à leur tour selon les évolutions futures.
>
> Concrètement, cela se traduit par :
>
> - un **nouveau dépôt UML** : `calypsonet-terminal-definitions-uml-api` (créé, version `1.0.0-SNAPSHOT`) ;
> - un **nouveau module Java Keypop** : `keypop-definitions-jvm-api` (à créer, conformément à la convention de nommage Keypop) ;
> - une **dépendance** déclarée du module `keypop-reader-java-api` vers ce nouveau module.

---

## Diagrammes UML de référence

Les diagrammes UML sont disponibles sur la branche **`develop`** de chacun des dépôts CNA hébergés sur [github.com/calypsonet](https://github.com/calypsonet/). Pour chaque dépôt (sauf la Terminal Definitions API qui est nouvelle), **deux variantes** sont fournies :

- **Diagramme final** (`api_class_diagram.svg`) — version **nettoyée** de tous les éléments barrés, en rouge et en gris ; c'est ce qui sera publié à la sortie de la 3.0.0.
- **Diagramme avec diff** (`api_class_diagram_diff.svg`) — version **conservant** les éléments barrés, en rouge et en gris pour permettre la **lecture du delta** entre la 2.x et la 3.0.0.

Les liens ci-dessous pointent directement vers les fichiers SVG, qui se rendent visuellement dans l'interface GitHub.

| Module | Version | Diagramme final | Diagramme avec diff |
|---|---|---|---|
| **Terminal Reader API** | 3.0.0-SNAPSHOT | [SVG final](https://github.com/calypsonet/calypsonet-terminal-reader-uml-api/blob/develop/3.0.0-SNAPSHOT/api_class_diagram.svg) | [SVG avec diff](https://github.com/calypsonet/calypsonet-terminal-reader-uml-api/blob/develop/3.0.0-SNAPSHOT/api_class_diagram_diff.svg) |
| **Terminal Card API** *(interne)* | 3.0.0-SNAPSHOT | [SVG final](https://github.com/calypsonet/calypsonet-terminal-card-uml-api/blob/develop/3.0.0-SNAPSHOT/api_class_diagram.svg) | [SVG avec diff](https://github.com/calypsonet/calypsonet-terminal-card-uml-api/blob/develop/3.0.0-SNAPSHOT/api_class_diagram_diff.svg) |
| **Terminal Calypso Card API** | 3.0.0-SNAPSHOT | [SVG final](https://github.com/calypsonet/calypsonet-terminal-calypso-card-uml-api/blob/develop/3.0.0-SNAPSHOT/api_class_diagram.svg) | [SVG avec diff](https://github.com/calypsonet/calypsonet-terminal-calypso-card-uml-api/blob/develop/3.0.0-SNAPSHOT/api_class_diagram_diff.svg) |
| **Terminal Definitions API** *(nouveau)* | 1.0.0-SNAPSHOT | [SVG final](https://github.com/calypsonet/calypsonet-terminal-definitions-uml-api/blob/develop/1.0.0-SNAPSHOT/api_class_diagram.svg) | — *(API nouvelle, pas de diff)* |

> Les sources PlantUML (`.puml`) sont également disponibles dans les mêmes dossiers de chaque dépôt, pour ceux qui souhaitent regénérer le rendu ou inspecter les annotations.

---

> Périmètre : **Reader API**, **Card API** et **Calypso Card API** — auxquelles s'ajoute, en tant que **nouvelle API socle**, la **Terminal Definitions API** (cf. encadré du préambule et Thème 6).
>
> **Conventions de lecture des diagrammes** :
> - **(diff uniquement)** Les éléments **barrés** (`<s>`) dans les diagrammes `_diff` ne sont conservés que pour faciliter la lecture du delta entre la 2.x et la 3.0.0. Ils **n'apparaissent pas** dans les diagrammes finaux.
> - **(diff uniquement)** Les éléments en **bleu** sont les **ajouts** de la 3.0.0.
> - **(diff uniquement)** Les éléments en **gris** (`<color:grey>`) sont des éléments **en cours d'étude** (« work in progress ») qui n'avaient jamais été implémentés dans Keypop Java. La 3.0.0 fait le ménage : ces éléments **n'apparaissent pas** dans les diagrammes finaux. Ils sont mentionnés ici quand cela aide à comprendre la trajectoire d'une notion (par exemple le retrait du `MultichannelCardSelector` expérimental au profit du nouveau modèle multicanal porté par le `CardSelectionManager`).
> - **(final et diff)** Les relations UML notées `+-` (composition par agrégation) indiquent que l'élément pointé est une **classe interne** (`inner class` / type imbriqué) du conteneur — par exemple `CardReaderEvent.Type` est une énumération interne de `CardReaderEvent`, et `IsoCardSelector.FileOccurrence` / `IsoCardSelector.FileControlInformation` sont des énumérations internes d'`IsoCardSelector`. Cette convention conditionne les imports et la nomenclature Java.

---

## Table des matières

0. [Diagrammes UML de référence](#diagrammes-uml-de-référence)
1. [Vue d'ensemble](#1-vue-densemble)
2. [Thème 1 — Support des canaux logiques multiples](#2-thème-1--support-des-canaux-logiques-multiples)
3. [Thème 2 — Contre-mesure de la faille de sécurité par attaque relai](#3-thème-2--contre-mesure-de-la-faille-de-sécurité-par-attaque-relai)
4. [Thème 3 — Simplification de la gestion de l'observation](#4-thème-3--simplification-de-la-gestion-de-lobservation)
5. [Thème 4 — Connaissance de l'état courant de la session sécurisée](#5-thème-4--connaissance-de-létat-courant-de-la-session-sécurisée)
6. [Thème 5 — Améliorations sémantiques (renommages et migration de concepts)](#6-thème-5--améliorations-sémantiques-renommages-et-migration-de-concepts)
7. [Thème 6 — Typage strict des technologies RF et des types de carte (support ECP)](#7-thème-6--typage-strict-des-technologies-rf-et-des-types-de-carte-support-ecp)
8. [Procédure de migration](#8-procédure-de-migration)
9. [Suite et validation par le TC Terminal](#9-suite-et-validation-par-le-tc-terminal)

---

## 1. Vue d'ensemble

La version 3.0.0 est une **version majeure** : elle introduit des ruptures de compatibilité sur les trois APIs existantes et **crée une nouvelle API socle** (`Terminal Definitions API`). Les changements sont motivés par six grands chantiers :

| # | Thème | Reader | Card | Calypso Card | Definitions |
|---|---|:---:|:---:|:---:|:---:|
| 1 | Canaux logiques multiples | ● | ● | ● | — |
| 2 | Contre-mesure attaque relai | — | ● | ● | — |
| 3 | Simplification observation | ● | — | — | — |
| 4 | État courant de la session sécurisée | — | — | ● | — |
| 5 | Améliorations sémantiques | ● | ● | ● | — |
| 6 | Typage RF / types de carte (ECP) | ● | — | — | ● (création) |

Les conséquences transverses :

- **Suppression de tout l'héritage déprécié** : la version majeure 3.0.0 est l'occasion de faire le ménage. Tous les éléments qui avaient été marqués `@Deprecated` dans les versions 1.x ou 2.x — y compris ceux qui n'avaient été dépréciés que tardivement — sont supprimés sans alternative de compatibilité. Plus aucun élément déprécié ne subsiste dans la 3.0.0.
- **Suppression des éléments « work in progress »** (en gris, `<color:grey>`). Ils figuraient dans les diagrammes uniquement à titre prospectif. Sont concernés :
  - **Reader API (gris en 2.x, retirés en 3.0.0)** : `ReaderApiFactory.createMultichannelCardSelector()`, l'interface `MultichannelCardSelector` et sa méthode `useDedicatedLogicalChannel()`, ainsi que `CardSelectionResult.getCardReader()`. La notion qu'ils préfiguraient (multicanal) est traitée proprement par le nouveau modèle décrit au §2.
  - **Calypso Card API (encore gris dans le snapshot 3.0.0)** : `AsymmetricCryptoSecuritySetting.authorizeAllTrustedCa()`, `authorizeOnlyConfiguredCa()` et `revokeCa(byte[])`. Ces méthodes de gestion fine de la révocation PKI sont laissées en gris dans le diagramme courant : elles **n'apparaîtront pas dans la version finale** 3.0.0 publiée.
- Disparition complète de l'énum `ChannelControl` (Reader API et Card API). La fermeture du canal n'est plus pilotée par un paramètre passé aux commandes, mais devient une **opération explicite** sur les nouvelles méthodes dédiées (`closeChannel`, `transmitCardRequestAndCloseChannel`, `processCommandsAndCloseChannel`).
- Disparition de `prepareReleaseChannel()` côté Reader API et de `releaseChannel()` côté Card API : la libération du canal devient un effet de bord du nouveau modèle multi-canal et n'a plus besoin d'API dédiée.
- Disparition de l'exception `InvalidCardResponseException` du package `selection` (encore présente comme classe « ancienne » dans le diagramme à titre informatif), au profit de l'exception du package racine `reader`.

---

## 2. Thème 1 — Support des canaux logiques multiples

### 2.1 Motivation

Jusqu'en 2.x, l'API supposait implicitement qu'un seul canal logique était ouvert à la fois entre le terminal et la carte. La 3.0.0 introduit explicitement la notion de **canaux logiques multiples** (cartes ISO supportant plusieurs sélections d'application simultanées sur des canaux logiques distincts), avec deux objectifs :

1. permettre de sélectionner et de manipuler **plusieurs applications** d'une même carte en parallèle ;
2. distinguer clairement, pour chaque carte intelligente, **sur quel canal** elle est attachée et **si elle est encore active**.

Le déclencheur concret de ce chantier est l'arrivée d'un nouveau produit CNA, **OpenSAM**, dont le modèle d'usage repose sur la **cohabitation simultanée de plusieurs applications de sécurité** accessibles en parallèle sur des canaux logiques distincts. Sans support multicanal explicite dans l'API, l'intégration d'OpenSAM dans Keypop nécessiterait un contournement côté implémentation ; la 3.0.0 lui fournit l'infrastructure idiomatique. Les spécifications détaillées d'OpenSAM relèvent de la documentation CNA dédiée à ce produit.

### 2.2 Reader API

#### Sélection multi-canal

- **Nouvelle méthode** `CardSelectionManager.processMultichannelCardSelectionScenario(CardReader, ChannelSelectionPolicy)` — équivalent multicanal de `processCardSelectionScenario`. **Prérequis** : la carte présentée doit supporter le multicanal ; à défaut, une exception est levée à l'exécution du scénario de sélection.
- **Nouvelle énumération** `ChannelSelectionPolicy` :
  - `ALLOW_BASIC_CHANNEL` — autorise l'usage du canal de base (canal 0) en plus des canaux logiques additionnels ;
  - `LOGICAL_CHANNEL_ONLY` — restreint la sélection aux canaux logiques supplémentaires (le canal 0 n'est pas utilisé).

#### Gestion fine du résultat de sélection

- **Nouvelle méthode** `CardSelectionResult.getActiveSelectionIndexes() : List<Integer>` — retourne **tous** les index de sélections actives (une par canal). La méthode existante `getActiveSelectionIndex()` est conservée pour la compatibilité du cas mono-canal.
- **Précision sémantique** sur `getActiveSmartCard()` (notée dans le diagramme) : en cas de plusieurs cartes actives, c'est la carte active sur le **canal 0** qui est retournée ; il faut désormais utiliser `getActiveSelectionIndexes()` pour obtenir l'ensemble des sélections actives.

#### Connaissance du canal au niveau de la carte

- **Nouvelle méthode** `SmartCard.isActive() : boolean` — la carte sait si elle est encore active sur son canal.
- **Nouvelle méthode** `IsoSmartCard.isBasicChannel() : boolean` — indique si la carte est attachée au canal de base ou à un canal logique supplémentaire.

#### Gestion transactionnelle multi-canal

- **Nouvelle interface** `MultichannelCardTransactionManager<T>` (dans `reader.transactionApi.spi`) qui étend `CardTransactionManager<T>` et ajoute :
  - `T processCommandsAndCloseChannel()` — exécute les commandes en attente et **ferme le canal** dans la foulée ;
  - `T closeChannel()` — fermeture explicite du canal.

> Côté `CardTransactionManager`, `processCommands(ChannelControl)` est remplacé par `processCommands()` sans paramètre : la fermeture du canal est désormais déléguée au nouveau gestionnaire multicanal.

### 2.3 Card API

- **Nouvelle méthode** `SmartCardSpi.deactivate()` — permet au `ProxyReaderApi` de **désactiver** la SmartCard de son côté, de telle sorte que l'application cliente, si elle conserve une référence vers le `SmartCard` correspondant, voie immédiatement son `isActive()` passer à `false`.
- **Nouvelle interface SPI** `MultichannelSmartCardSpi extends SmartCardSpi` avec `int getChannel()` — la SmartCard SPI multicanal expose son canal d'attachement.
- **Refonte de `ProxyReaderApi`** :
  - **Supprimées** : `transmitCardRequest(CardRequestSpi, ChannelControl)` et `releaseChannel()`.
  - **Ajoutées** :
    - `CardResponseApi transmitCardRequest(CardRequestSpi, SmartCardSpi)` — la cible APDU est désignée par la SmartCard (cf. §2.3.1 ci-dessous) ;
    - `CardResponseApi transmitCardRequestAndCloseChannel(CardRequestSpi, MultichannelSmartCardSpi)` — variante atomique « exécuter + fermer » ;
    - `void closeChannel(MultichannelSmartCardSpi)` — fermeture ciblée d'un canal donné.
- **Ajout** : `CardSelectionResponseApi.getChannel() : int` — la réponse de sélection porte désormais l'information du canal.
- **Suppression** : `CardResponseApi.isLogicalChannelOpen()` — devient redondante avec le nouveau modèle où le canal est une propriété de la SmartCard SPI.

#### 2.3.1 Rôle de la SmartCard (S)PI transmise au `ProxyReaderApi`

Les trois nouvelles méthodes du `ProxyReaderApi` reçoivent une SmartCard (S)PI en paramètre — `SmartCardSpi` pour `transmitCardRequest(...)`, `MultichannelSmartCardSpi` pour `transmitCardRequestAndCloseChannel(...)` et `closeChannel(...)`. Ce paramètre n'est **pas** un simple véhicule pour le numéro de canal. Il joue jusqu'à trois rôles :

1. **Transporter le numéro de canal logique** (uniquement avec `MultichannelSmartCardSpi`, via `getChannel()`) sur lequel doit être adressée la requête APDU ou la fermeture.
2. **Transporter l'état actif/non-actif de la SmartCard** afin que le `ProxyReaderApi` puisse **vérifier en amont** que la carte est encore active avant d'émettre toute requête. Cela protège l'application contre l'usage involontaire d'une SmartCard dont l'état réel ne correspond plus à ce que pense le code appelant (carte retirée, canal fermé par une autre voie, exception de communication antérieure, etc.).
3. **Permettre au `ProxyReaderApi` de désactiver la SmartCard** (`SmartCardSpi.deactivate()`) si la situation l'exige — typiquement après une exception de communication ou à la fermeture explicite du canal — pour que cette désactivation soit immédiatement visible côté application via `SmartCard.isActive() == false`.

Les rôles 2 et 3 sont communs à `SmartCardSpi` et `MultichannelSmartCardSpi` ; le rôle 1 est spécifique aux variantes multicanal.

#### 2.3.2 Cycle de vie des `SmartCard` mémorisées par le `CardReader`

Pour que ce contrat d'« état réel reflété » fonctionne, le `CardReader` **mémorise temporairement les références vers les `SmartCard` issues de la dernière sélection** (jusqu'à la prochaine étape qui invalide leur état). Cette mémorisation permet au `CardReader` de **désactiver automatiquement** les SmartCards concernées dans les quatre cas suivants :

1. lors d'une **nouvelle sélection en mode mono-canal** (les anciennes SmartCards deviennent obsolètes) ;
2. lors d'une **demande explicite de fermeture du canal** (`closeChannel`, `transmitCardRequestAndCloseChannel`, `processCommandsAndCloseChannel`) ;
3. lors de l'appel à **`ObservableCardReader.endCardProcessing()`** (fin du traitement de la carte courante) ;
4. lors d'une **exception indiquant que la carte n'est plus contactable** (typiquement `CardCommunicationException` / `ReaderCommunicationException` au niveau Reader, ou `CardBrokenCommunicationException` / `ReaderBrokenCommunicationException` au niveau Card API).

L'objectif est que, dans tous ces cas, **toute tentative ultérieure d'utiliser une SmartCard désormais invalide dans une transaction** (Calypso ou autre) puisse être interceptée par le `ProxyReaderApi` au moment de la vérification de `isActive()` — au lieu de produire une erreur plus tardive et plus difficile à diagnostiquer.

**Durée de vie de la mémorisation** : le `CardReader` conserve les références aux SmartCards depuis leur création (résultat de la sélection) jusqu'à l'un des quatre événements ci-dessus. Concrètement, à l'appel à `endCardProcessing()` (cas le plus fréquent), les SmartCards sont désactivées **et** leurs références sont libérées par le `CardReader` ; elles peuvent alors être finalisées par le ramasse-miettes Java si l'application n'en conserve pas de référence propre.

> Cette responsabilité incombe à l'**implémentation** du `CardReader` et du `ProxyReaderApi` : l'API publique se contente d'exposer `SmartCard.isActive()` et `SmartCardSpi.deactivate()` ; le contrat « mémorisation + désactivation aux quatre points ci-dessus » doit être documenté dans la Javadoc du module Java mais n'apparaît pas explicitement dans le diagramme UML.

### 2.4 Calypso Card API

- **Nouvelle méthode** `TransactionManager.getLogicalChannelSupport() : MultichannelTransactionManager` — point d'entrée vers les opérations multicanal côté Calypso.
- **Nouvelle interface** `MultichannelTransactionManager` (mappée sur `MultichannelCardTransactionManager` de la Reader API).

### 2.5 Justification

Le contrôle « par paramètre » (`ChannelControl.KEEP_OPEN` / `CLOSE_AFTER`) reposait sur une notion implicite et globale d'« unique canal courant ». Dans un contexte multi-canal, ce modèle est ambigu : sur quel canal s'applique le `CLOSE_AFTER` ? Le passage à un modèle où la cible (la `SmartCard(Spi)`) est **explicitement nommée** dans chaque appel résout cette ambiguïté et rend l'API auto-descriptive.

---

## 3. Thème 2 — Contre-mesure de la faille de sécurité par attaque relai

### 3.1 Motivation

Une **attaque relai** consiste à intercaler un attaquant entre la carte et le terminal et à relayer les échanges APDU vers une carte distante, ce qui rend possible une opération frauduleuse à l'insu du porteur. La 3.0.0 introduit un mécanisme de **mesure et de bornage des durées d'échange** APDU et de **bornage de la durée de session sécurisée**, qui permet au framework de détecter l'allongement anormal des temps de communication caractéristique d'un relai.

#### Modèle de menace retenu

- **Surface d'attaque visée** : **attaque applicative** (relai logiciel des APDU au sein de l'environnement applicatif), par opposition à des attaques au niveau du transport RF physique qui relèvent de contre-mesures matérielles.
- **Ordre de grandeur** des bornes pertinentes : la **milliseconde** (`ms`). Les durées effectives d'échange et de session sont mesurées dans cette unité, et les bornes définies par l'intégrateur s'expriment dans la même unité.
- **Lieu de mesure** : l'**implémentation de la Terminal Reader API** (couche framework Keypop côté terminal). C'est elle qui mesure la durée effective de chaque échange APDU et de chaque session sécurisée, et qui compare cette mesure aux bornes déclarées par l'application.
- **Comportement post-dépassement** : si une borne est dépassée, la **session en cours est annulée automatiquement** par l'implémentation et une exception **`InvalidCardResponseException`** est levée, avec un message détaillé indiquant la borne dépassée et la durée mesurée. L'application peut ainsi distinguer ce cas des autres erreurs de communication et journaliser/alerter en conséquence.

### 3.2 Card API

- **Côté requête** : `ApduRequestSpi.getApduExchangeMaxDuration() : Long` — durée maximale tolérée pour l'échange APDU (en millisecondes). Le type retourné est volontairement `Long` (boxed) : la valeur `null` signifie **« pas de borne définie »** pour cette requête.
- **Côté réponse** : `ApduResponseApi.getApduExchangeDuration() : Long` — durée effective de l'échange, telle que mesurée par le lecteur. La valeur `null` signifie **« durée non mesurée »** (le lecteur ne fournit pas la mesure).
- **Nouvelle exception** `ApduExchangeDurationExceededException extends AbstractApduException` — levée par `ProxyReaderApi.transmitCardRequest(...)` (Card API, niveau interne) lorsque la durée effective de l'échange dépasse la borne `getApduExchangeMaxDuration()` déclarée sur la requête (sans effet si la borne est `null`). Côté **intégrateur**, cette exception est interceptée par l'implémentation de la Terminal Reader API, qui **annule automatiquement la session en cours** et propage l'événement sous forme d'`InvalidCardResponseException` enrichi du détail (cf. §3.1, modèle de menace).

### 3.3 Calypso Card API

Toutes les nouvelles méthodes prennent un paramètre `csnMin` qui est un **seuil** sur le CSN (Card Serial Number) : la règle s'applique à **toute carte dont le CSN est supérieur ou égal à `csnMin`**. Ce mécanisme permet d'introduire ou de durcir progressivement les bornes de durée au fur et à mesure que de nouvelles générations de cartes (au CSN plus élevé) sont émises, sans pénaliser les cartes plus anciennes.

> **Règle de combinaison des `csnMin`** : lorsque plusieurs appels à `assignOpenSecureSessionMaxDuration(...)` (ou `assignSvOperationMaxDuration(...)`) sont effectués avec des `csnMin` différents, chaque appel définit une **plage** délimitée par son `csnMin` et le `csnMin` immédiatement supérieur déclaré (ou +∞ pour la déclaration au seuil le plus élevé). Pour une carte donnée, c'est la **plage à laquelle son CSN appartient** qui détermine la borne appliquée. Exemple : `assignOpenSecureSessionMaxDuration(100, …, 200)` puis `assignOpenSecureSessionMaxDuration(500, …, 100)` définit deux plages — `[100, 499]` borne à 200 ms, `[500, +∞[` borne à 100 ms.

- **`SymmetricCryptoSecuritySetting`** — quatre nouvelles méthodes :
  - `assignOpenSecureSessionMaxDuration(long csnMin, byte[] dfName, long maxDuration)` — durée max d'une session sécurisée pour les cartes de CSN ≥ `csnMin` et un DF cible ;
  - `assignOpenSecureSessionMaxDuration(long csnMin, long maxDuration)` — variante tous DF ;
  - `assignSvOperationMaxDuration(long csnMin, byte[] dfName, long maxDuration)` — borne dédiée aux opérations Stored Value (SV) ;
  - `assignSvOperationMaxDuration(long csnMin, long maxDuration)` — variante tous DF.
- **`AsymmetricCryptoSecuritySetting`** — deux nouvelles méthodes :
  - `assignOpenSecureSessionMaxDuration(long csnMin, byte[] dfName, long maxDuration)` ;
  - `assignOpenSecureSessionMaxDuration(long csnMin, long maxDuration)`.

### 3.4 Justification

Une attaque relai introduit un délai significatif et systématique sur les échanges APDU ; surveiller ce délai au niveau du lecteur (Card API) et au niveau de la session Calypso (Calypso Card API) permet de couvrir aussi bien la lutte au coup-par-coup que la lutte au niveau du protocole transactionnel sécurisé.

---

## 4. Thème 3 — Simplification de la gestion de l'observation

### 4.1 Motivation

Le modèle 2.x exposait un **patron Observateur** complet (`addObserver`, `removeObserver`, `clearObservers`, `countObservers`, `setReaderObservationExceptionHandler`) auquel s'ajoutaient deux SPI distinctes (`CardReaderObserverSpi` pour les événements et `CardReaderObservationExceptionHandlerSpi` pour les erreurs). En pratique, dans tous les cas d'usage réels, **un seul observateur** est enregistré, et la séparation entre gestionnaire d'événements et gestionnaire d'erreurs n'apportait pas de valeur (les deux étaient implémentés ensemble la plupart du temps).

### 4.2 Reader API — `ObservableCardReader`

**Méthodes supprimées** :

- `setReaderObservationExceptionHandler(CardReaderObservationExceptionHandlerSpi)`
- `addObserver(CardReaderObserverSpi)`
- `removeObserver(CardReaderObserverSpi)`
- `clearObservers()`
- `countObservers()`
- `startCardDetection(DetectionMode)` (signature à un argument)

**Méthodes ajoutées / refondues** :

- `startCardDetection(CardDetectionSettings settings, CardReaderEventHandler eventHandler)` — l'enregistrement de l'observateur (et sa déclaration) se fait désormais **au moment où la détection démarre**, en une seule opération. Le premier paramètre n'est plus un simple `DetectionMode` mais un objet `CardDetectionSettings` (builder unique), qui transporte le mode de détection **et** les autres caractéristiques (technologies RF ciblées, trame ECP, etc.). Cette refonte est détaillée au Thème 6 (cf. §7) ; on retient ici simplement que l'observation et la paramétrisation de la détection sont désormais co-localisées sur un même appel.

**SPI supprimées** :

- `CardReaderObserverSpi`
- `CardReaderObservationExceptionHandlerSpi`

**SPI ajoutée** :

- `CardReaderEventHandler` qui **fusionne** les deux SPI précédentes :
  - `void onReaderEvent(CardReaderEvent cardReaderEvent)`
  - `void onReaderError(String context, String readerName, Throwable e)`

### 4.3 Justification

Cette simplification :

1. **Réduit la surface de l'API** (5 méthodes et 2 SPI deviennent 1 méthode et 1 SPI) ;
2. **Élimine les états invalides** (observer enregistré sans handler d'erreurs, détection démarrée sans observer, etc.) ;
3. **Aligne l'API** sur l'usage réel observé chez les intégrateurs.

---

## 5. Thème 4 — Connaissance de l'état courant de la session sécurisée

### 5.1 Motivation

Côté Calypso, l'application cliente n'avait pas de moyen direct, à un instant donné, de savoir **si une session sécurisée était ouverte** ni d'en connaître la **nature** (symétrique / asymétrique) ou le **niveau d'accès en écriture** demandé à l'ouverture. C'était une information utile pour les couches métiers, notamment pour décider d'enchaîner ou non certaines préparations de commandes.

### 5.2 Calypso Card API

- **Nouvelle méthode** `TransactionManager.getSecureSessionStatus() : SecureSessionStatus` — accesseur de l'état courant. L'objet retourné est un **snapshot immuable** capturé au moment de l'appel ; il ne reflète pas les changements ultérieurs de l'état de la session. Pour obtenir un état rafraîchi, l'appelant doit ré-invoquer `getSecureSessionStatus()`.
- **Nouvelle interface** `SecureSessionStatus` :
  - `boolean isOpen()` — une session sécurisée est-elle ouverte à l'instant du snapshot ?
  - `SecureSessionType getType()` — nature cryptographique de la session ;
  - `WriteAccessLevel getWriteAccessLevel()` — niveau d'écriture demandé à l'ouverture. Cette méthode retourne **`null` en mode PKI** (`SecureSessionType.ASYMMETRIC`), où l'ouverture de session se fait via `prepareOpenSecureSession()` sans `WriteAccessLevel`.
- **Nouvelle énumération** `SecureSessionType` :
  - `SYMMETRIC` (sessions PSO/SAM, modes Regular et Extended) ;
  - `ASYMMETRIC` (mode PKI).

> **Granularité retenue pour `SecureSessionType`** : l'énumération reflète la **nature cryptographique** de la session (symétrique vs asymétrique) et non le **mode applicatif** (Regular / Extended). Ces deux modes côté symétrique partagent la même cryptographie sous-jacente et ne se distinguent que par leur configuration au niveau du `TransactionManager` (cf. les sous-types `SecureRegularModeTransactionManager` / `SecureExtendedModeTransactionManager`). Un intégrateur qui a besoin de connaître le mode applicatif l'obtient directement via le sous-type du `TransactionManager` qu'il a instancié ; `SecureSessionStatus` se concentre sur l'information stratégique « symétrique ou asymétrique » qui conditionne le périmètre des opérations possibles (clés, certificats, etc.).

### 5.3 Justification

Le `TransactionManager` Calypso est désormais **introspectable** sur sa propre session sécurisée, ce qui :

- évite que l'appelant ait à maintenir en parallèle son propre suivi d'état ;
- rend explicites des informations précédemment implicites (le type de session est aujourd'hui déduit du sous-type de `SecureTransactionManager` instancié — sur lequel l'appelant a perdu prise dès lors qu'il manipule la généricité `T extends TransactionManager<T>`).

---

## 6. Thème 5 — Améliorations sémantiques (renommages et migration de concepts)

### 6.1 Reader API

#### Renommages d'énumérations et de valeurs

| Avant (2.x) | Après (3.0.0) | Justification |
|---|---|---|
| `DetectionMode.SINGLESHOT` | `DetectionMode.SINGLE_SHOT` | Conformité à la convention de nommage `SCREAMING_SNAKE_CASE` (mot composé). |
| `CardReaderEvent.Type.UNAVAILABLE` | `CardReaderEvent.Type.READER_UNREGISTERED` | Le nom décrit désormais la **cause** réelle de l'événement (le lecteur a été désenregistré) et non plus une conséquence vague (« indisponible »). |
| `NotificationMode` (package `reader`) | `CardPresenceNotificationPolicy` (package `reader.selection`) | Le nom décrit ce qui est notifié (présence de carte) ; le déplacement de package reflète que cette notion appartient à la **sélection** et non au lecteur. |
| `ObservableCardReader.finalizeCardProcessing()` | `ObservableCardReader.endCardProcessing()` | `finalize` est un mot réservé chargé en Java (méthode de `Object`, désormais dépréciée par le JDK) ; `end` est plus neutre et plus court. |

#### Migration du concept « mode de sélection multiple »

- **Supprimée** : `CardSelectionManager.setMultipleSelectionMode()` — méthode à effet de bord qui modifiait silencieusement le comportement du `process...` suivant.
- **Remplacée** par un **paramètre explicite** `SelectionExecutionPolicy` passé à `processCardSelectionScenario` et `scheduleCardSelectionScenario` :
  - `STOP_ON_FIRST_MATCH` — comportement par défaut historique ;
  - `PROCESS_ALL` — équivalent du mode multiple précédent.

Conséquence sur les signatures :

```text
2.x : processCardSelectionScenario(CardReader)
3.0 : processCardSelectionScenario(CardReader, SelectionExecutionPolicy)

2.x : scheduleCardSelectionScenario(ObservableCardReader, NotificationMode)
3.0 : scheduleCardSelectionScenario(ObservableCardReader,
                                    CardPresenceNotificationPolicy,
                                    SelectionExecutionPolicy)
```

#### Simplifications de l'arbre des sélecteurs

- **Suppression** de l'interface intermédiaire `CommonIsoCardSelector<T>` (2.1.0).
- `IsoCardSelector` étend désormais directement `CardSelector` et porte les méthodes `filterByDfName`, `setFileOccurrence`, `setFileControlInformation`.

#### Disparitions liées au modèle de canal

- **Supprimée** : `CardSelectionManager.prepareReleaseChannel()`.
- **Supprimée** : `ChannelControl` (énumération) — cf. Thème 1.
- **Supprimée** : `CardTransactionManager.processCommands(ChannelControl)`, remplacée par `processCommands()` sans paramètre.

#### Disparitions liées à la configuration de protocole par chaînes libres

Ces évolutions sont **détaillées au Thème 6** (cf. §7) et listées ici à titre récapitulatif :

- **Supprimée** : interface `ConfigurableCardReader` (et ses méthodes `activateProtocol`, `deactivateProtocol`, `getCurrentProtocol`). Le mécanisme d'activation de protocole par chaînes libres disparaît entièrement.
- **Supprimée** : `CardSelector.filterByCardProtocol(String logicalProtocolName)`, remplacée par `filterByCardType(CardType)` typé (cf. §7.3).

### 6.2 Card API

- **Suppression** : `ChannelControl` (cohérence avec la Reader API).
- **Suppression** : `ProxyReaderApi.releaseChannel()`.
- **Suppression** : `CardResponseApi.isLogicalChannelOpen()`.

### 6.3 Calypso Card API

#### Suppressions

- `TransactionManager.processCommands(ChannelControl)` — alignement avec la Reader API et la Card API.
- Exceptions retirées :
  - `UnexpectedCommandStatusException` — couverte par `InvalidCardResponseException` au niveau Reader ;
  - `ReaderIOException` — couverte par `ReaderCommunicationException` au niveau Reader ;
  - `CardIOException` — couverte par `CardCommunicationException` au niveau Reader.

  Justification : ces trois exceptions Calypso doublonnaient des exceptions de niveau Reader / Card. Elles sont retirées au profit de la hiérarchie d'exceptions de niveau inférieur, déjà propagée naturellement.

#### Cohérence avec les renommages Reader API

- L'interface `CardTransactionManager` (Reader API) reste référencée par stéréotype sur `TransactionManager` (Calypso) ; la nouvelle interface multicanal `MultichannelCardTransactionManager` est elle aussi référencée par stéréotype sur le nouveau `MultichannelTransactionManager` Calypso. Les renommages internes à la Reader API se propagent ainsi par stéréotype dans le diagramme Calypso, sans nécessiter de modification supplémentaire côté Calypso Card API.

---

## 7. Thème 6 — Typage strict des technologies RF et des types de carte (support ECP)

### 7.1 Motivation

Deux moteurs convergent et justifient ce chantier :

1. **Fin des chaînes libres pour les protocoles**. Les versions 1.x et 2.x configuraient l'activation de protocoles et le filtrage de sélection à l'aide de **chaînes de caractères** (`String physicalProtocolName`, `String logicalProtocolName`). Cette approche posait plusieurs problèmes : valeurs non normalisées d'un intégrateur à l'autre, fautes de frappe non détectées à la compilation, documentation dispersée des « bonnes » valeurs à utiliser, impossibilité pour l'IDE d'aider l'intégrateur, absence de découverte des cas supportés.
2. **Arrivée du support ECP** (Enhanced Contactless Polling). ECP est un mécanisme défini par la **spécification Apple ECP** permettant la détection rapide de cartes (notamment Calypso) en mode transit sur iPhone, qui nécessite l'envoi d'une **trame de polling spécifique** au moment du démarrage de la détection. La trame elle-même est traitée comme une donnée binaire opaque (`byte[]`) construite par l'application conformément à la spécification Apple, et transmise telle quelle par le framework au lecteur. Ce besoin n'avait pas de représentation propre dans l'API 2.x : le `startCardDetection` ne prenait qu'un `DetectionMode`, sans moyen de transporter une trame ECP ni de cibler des technologies RF particulières.

La conjonction des deux a déclenché la refonte. ECP est l'occasion concrète qui justifie d'en finir maintenant avec le système de protocoles par chaînes libres, en introduisant à la fois :

- des **énumérations fortement typées** (`RfTechnology`, `CardType`) qui remplacent les chaînes ;
- une **interface de paramètres de détection** (`CardDetectionSettings`) extensible au format builder, qui rend possible le support d'ECP sans rompre la signature du moment où d'autres mécanismes de polling sophistiqués apparaîtront.

### 7.2 Nouvelle API socle — Terminal Definitions API

Les énumérations `RfTechnology` et `CardType` **ne sont pas définies dans la Reader API** : elles sont placées dans une **nouvelle API socle** créée à l'occasion de la 3.0.0, la **Terminal Definitions API**.

#### Rôle et vocation

La Terminal Definitions API n'expose ni interfaces de service, ni SPI, ni machines à états. Sa seule vocation est d'héberger les **types énumérés transverses** qui constituent des **constantes globales** partagées entre plusieurs APIs Terminaux. À sa création, elle contient les deux énumérations introduites par le Thème 6 ; mais elle est **destinée à accueillir progressivement** les autres énumérations transverses au fil des évolutions futures (par exemple, on peut imaginer demain y migrer des notions actuellement dispersées comme `FileOccurrence`, `FileControlInformation`, ou de futures énumérations partagées entre Reader, Card et Calypso).

#### Conséquences structurelles

- **Nouveau dépôt UML** : `calypsonet-terminal-definitions-uml-api`, créé sur le même modèle d'organisation que les autres dépôts `calypsonet-terminal-*-uml-api`, avec son propre `api_class_diagram.puml` (version `1.0.0-SNAPSHOT`) et son cycle de versionnement indépendant.
- **Nouveau module Java Keypop** : `keypop-definitions-jvm-api` (à créer).
- **Dépendance** : la Terminal Reader API 3.0.0 **dépend** de la Terminal Definitions API. Cette dépendance est :
  - **publique** (les enums apparaissent dans les signatures publiques de `CardSelector.filterByCardType`, `CardDetectionSettings.setRfTechnologies`, `CardSelectionResult.getCardType`, etc.) ;
  - **transitive** côté Java (les consommateurs de la Reader API obtiennent automatiquement l'accès aux enums sans déclaration supplémentaire).
- Les autres APIs Terminaux (Card, Calypso Card) ne dépendent **pas** de la Terminal Definitions API à ce stade, mais pourront le faire à l'avenir si une énumération transverse les concerne.

#### Justification de la création d'une API dédiée plutôt que d'un sous-package de la Reader API

Plusieurs raisons motivent l'extraction dans un module à part :

1. **Réutilisabilité transverse** : `RfTechnology` et `CardType` (et les futurs enums) sont des notions de plus bas niveau que la Reader API. Les enfermer dans la Reader API forcerait toute autre API Terminale qui voudrait les référencer à dépendre de la Reader API entière.
2. **Stabilité** : les constantes globales évoluent à un rythme propre (en général très lent et rétro-compatible). Les isoler dans un module dédié leur permet d'avoir leur propre cycle de versionnement, sans embarquer le bruit des changements de la Reader API.
3. **Lisibilité** : la Terminal Definitions API agit comme un point d'entrée unique et documenté pour les constantes globales, plutôt que d'éparpiller ces notions dans des packages `…/definitions` au sein de chaque API.

#### Contenu initial de la Terminal Definitions API

Le dépôt UML `calypsonet-terminal-definitions-uml-api` a été créé et embarque, dans sa version `1.0.0-SNAPSHOT`, un diagramme avec :

- **`DefinitionsApiProperties`** — classe portant la constante `VERSION` du module (sur le même modèle que `ReaderApiProperties`, `CardApiProperties`, etc.) ;
- **`RfTechnology`** — technologies de communication radio supportées :
  - `ISO_14443_AB`
  - `INNOVATRON_B_PRIME`
  - `FELICA`
  - `ISO_15693`
- **`CardType`** — types de cartes identifiables :
  - `ISO_7816_3` (cartes à contact)
  - `ISO_14443_4` (cartes sans contact ISO 14443-4, sans distinction A/B au niveau du type)
  - `ISO_14443_3A_MIFARE_CLASSIC_1K`, `ISO_14443_3A_MIFARE_CLASSIC_4K`, `ISO_14443_3A_MIFARE_ULTRALIGHT`
  - `ISO_14443_3B_ST25_SRT512`
  - `INNOVATRON_B_PRIME`, `FELICA`, `ISO_15693`
  - `UNKNOWN`

> **Note sur la granularité de `CardType` et l'asymétrie A/B avec `RfTechnology`** :
>
> - `RfTechnology` est une **donnée d'entrée** (polling) au niveau de la sous-couche RF : `ISO_14443_AB` fusionne A et B parce que, au niveau du polling, l'intégrateur déclare interroger « la famille ISO 14443 » sans avoir à distinguer la variante.
> - `CardType` est une **donnée de sortie** (résultat de la détection) qui combine niveau de protocole et identité produit :
>   - pour les standards de **transport** (ISO 14443-4, ISO 7816-3, ISO 15693), la sous-couche RF (3A ou 3B) ne change pas le comportement applicatif : une seule valeur `ISO_14443_4` suffit.
>   - pour les **produits propriétaires** où la sous-couche RF est sémantiquement liée au produit (MIFARE en 14443-3A, ST25 en 14443-3B), la granularité produit fait naturellement apparaître la variante (`ISO_14443_3A_MIFARE_CLASSIC_1K`, `ISO_14443_3B_ST25_SRT512`, etc.).
>
> Cette double granularité (transport vs produit) explique pourquoi A/B est fusionné côté input (`RfTechnology`) et au niveau du transport ISO 14443-4 (`CardType`), tout en restant distinguable au niveau des types produit.
>
> Les deux énumérations sont conçues pour évoluer : l'ajout d'une nouvelle valeur dans la Terminal Definitions API est une évolution **rétro-compatible** sur ses versions mineures.
>
> **Note sur `CardType.UNKNOWN`** : cette valeur est retournée par `CardSelectionResult.getCardType()` lorsque le framework n'a **pas pu identifier** le type de la carte détectée (carte d'un type non encore référencé dans l'énumération, ou détection partielle). Côté `CardSelector.filterByCardType(UNKNOWN)` permet à l'intégrateur de **capturer explicitement** ces cartes non identifiées plutôt que de les ignorer silencieusement.

### 7.3 Reader API — typage strict

#### Refonte de `CardSelector`

- **Supprimée** : `T filterByCardProtocol(String logicalProtocolName)`.
- **Nouvelle méthode** : `T filterByCardType(CardType cardType)` — filtre par type de carte, en utilisant l'énumération `CardType` de la Terminal Definitions API.

`CardType` est la donnée caractérisant ce qui a été détecté et est, à ce titre, le critère naturel de filtrage à la sélection : on filtre par **ce que la carte est**. La technologie RF associée est implicitement encodée par `CardType` (`ISO_14443_4` ⇒ ISO 14443-AB, `FELICA` ⇒ FELICA, etc.), ce qui rend redondant tout filtre supplémentaire sur `RfTechnology`.

#### Suppression intégrale de `ConfigurableCardReader`

L'interface `ConfigurableCardReader` et ses trois méthodes `activateProtocol(String, String)`, `deactivateProtocol(String)`, `getCurrentProtocol()` sont **entièrement supprimées** de la 3.0.0. Le mécanisme par chaînes libres n'a plus de place dans le modèle : la configuration des protocoles à activer côté lecteur passe par les paramètres typés transmis au démarrage de la détection (cf. §7.4).

### 7.4 Reader API — paramétrisation de la détection

#### Interface unique `CardDetectionSettings` (builder)

Toutes les informations de détection (mode, technologies RF, trame ECP) sont véhiculées par **une seule interface** au format builder :

```text
CardDetectionSettings
  ├── CardDetectionSettings setDetectionMode(DetectionMode detectionMode)
  ├── CardDetectionSettings setRfTechnologies(Set<RfTechnology> rfTechnologies)
  └── CardDetectionSettings setEcpFrame(byte[] ecpFrame)
```

- **`setDetectionMode`** — fixe le mode de détection (`REPEATING` ou `SINGLE_SHOT`). `DetectionMode` est désormais l'**énumération interne** de `CardDetectionSettings`.
- **`setRfTechnologies`** — déclare l'ensemble des technologies RF à activer pendant le polling. Non pertinent pour les lecteurs à contact (accepté sans effet).
- **`setEcpFrame`** — fournit la trame ECP à émettre au démarrage du polling. Pertinent uniquement sur les lecteurs supportant ECP.

L'instance se crée via la factory :

```text
ReaderApiFactory.createCardDetectionSettings() : CardDetectionSettings
```

#### Valeurs par défaut et tolérance des paramètres non supportés

- **Valeurs par défaut** appliquées si les setters correspondants ne sont pas invoqués :
  - `DetectionMode.REPEATING` pour le mode de détection ;
  - `{ RfTechnology.ISO_14443_AB }` pour le jeu de technologies RF.
- **Tolérance des paramètres non supportés** : si un paramètre fourni n'est pas supporté par le lecteur (par exemple une `RfTechnology` que le lecteur ne sait pas activer, ou une trame ECP transmise à un lecteur qui ne supporte pas ECP), le paramètre est **ignoré silencieusement** et un **message de niveau `WARN`** est journalisé par l'implémentation. **Aucune exception** n'est levée. Ce comportement permissif garantit qu'un déploiement hétérogène (terminaux ECP + terminaux non-ECP, parc mélangé) puisse partager le même code applicatif.

Le contrat complet est documenté en Javadoc côté implémentation.

### 7.5 Reader API — refonte de `ObservableCardReader`

`ObservableCardReader` est une interface **unique**, valable pour tous les types de lecteurs (contact, sans contact, ECP). Sa nouvelle signature de démarrage de détection est :

```text
2.x : void startCardDetection(DetectionMode detectionMode)
3.0 : void startCardDetection(CardDetectionSettings settings, CardReaderEventHandler eventHandler)
```

La signature transporte en un seul appel **l'observateur** (`CardReaderEventHandler`, cf. Thème 3) **et la configuration de détection** (`CardDetectionSettings`, cf. §7.4), supprimant ainsi les états intermédiaires invalides du modèle 2.x. La nature du lecteur se reflète à l'exécution par les setters utilisés sur `CardDetectionSettings` et par la cohérence interne de l'implémentation du lecteur.

#### Exposition du `CardType` détecté sur `CardSelectionResult`

L'information « type de carte détecté » est portée par le **résultat de sélection** (`CardSelectionResult`). Une nouvelle méthode y est ajoutée :

- `CardType getCardType()` — sur `CardSelectionResult`, retourne le type de carte (`CardType` de la Terminal Definitions API) effectivement détecté.

C'est l'unique getter de post-détection : `RfTechnology` ne fait pas l'objet d'un getter dédié, car elle est implicitement encodée par `CardType`.

Ce placement homogénéise la surface d'API : `CardSelectionResult` est le point d'accès aux informations issues de la détection / sélection (`getSmartCards()`, `getActiveSmartCard()`, `getActiveSelectionIndex…()`, `getCardType()`), et la durée de validité du `CardType` est intrinsèquement liée au cycle de vie du résultat de sélection.

### 7.6 Justification

- **Sécurité de compilation côté valeurs** : le passage des `String` (protocoles libres) aux enums (`RfTechnology`, `CardType`) élimine toute une classe de bugs (typos, valeurs propriétaires non documentées) et rend l'API auto-descriptive (auto-complétion IDE, Javadoc centralisée sur les enums).
- **Simplicité d'API** : un seul `ObservableCardReader`, un seul `CardDetectionSettings`. La surface d'API exposée à l'intégrateur est minimale et homogène quel que soit le type de lecteur ciblé.
- **Évolutivité** : `CardDetectionSettings` est ouvert — ajouter demain un nouveau paramètre de polling (nouvelle technique d'optimisation pour un nouveau standard, etc.) consistera à ajouter un setter à l'interface, **sans toucher** la signature de `startCardDetection` et sans casser la compatibilité binaire (l'ajout d'une méthode `default` à l'interface est rétro-compatible).
- **Séparation input / output** : `RfTechnology` est un paramètre d'**entrée** (polling, via `CardDetectionSettings.setRfTechnologies`) ; `CardType` est la donnée de **sortie** (résultat de la détection, exposée par `CardSelectionResult.getCardType()` et utilisable dans la sélection via `CardSelector.filterByCardType`).
- **Localisation cohérente des données** : le `CardType` détecté est exposé sur `CardSelectionResult`, qui regroupe l'ensemble des informations issues de la détection / sélection.
- **Déclaration unique et typée du protocole** : en 2.x, le protocole devait être déclaré deux fois (une fois côté lecteur via `activateProtocol`, une fois côté sélecteur via `filterByCardProtocol`). En 3.0.0, la déclaration est unique et typée (`CardDetectionSettings.setRfTechnologies` pour le polling, `CardSelector.filterByCardType` pour la sélection).
- **Réutilisabilité transverse** : l'extraction des enums dans la **Terminal Definitions API** (cf. §7.2) garantit que les futures APIs Terminales (ou les évolutions des APIs actuelles) pourront référencer ces constantes globales **sans induire une dépendance vers la Reader API**.

---

## 8. Procédure de migration

La migration du code applicatif depuis les versions 1.x ou 2.x vers la 3.0.0 fera l'objet d'un **guide technique de migration dédié**, publié séparément après validation de la 3.0.0 par le TC Terminal et après alignement des implémentations Java Keypop associées.

Ce guide aura pour objectif de **simplifier autant que possible la transition** vers la version 3.0.0 : correspondance 1:1 des éléments retirés / renommés / refondus, patrons de réécriture (`avant` / `après`) pour les cas d'usage les plus fréquents, règles d'adoption progressive et pièges connus.

---

## 9. Suite et validation par le TC Terminal

### 9.1 Périmètre soumis à validation

Le présent document soumet à la validation du **TC Terminal de la CNA** les éléments suivants :

1. **Le principe** des six thèmes d'évolution exposés (§2 à §7) et la cohérence d'ensemble du chantier 3.0.0.
2. **Les choix de conception** documentés dans les sections « Justification » de chaque thème — en particulier :
   - le modèle multicanal explicite reposant sur la `SmartCard(Spi)` comme cible nommée (§2.5) ;
   - le bornage de durée porté à la fois au niveau APDU et au niveau session Calypso, avec un filtrage par seuil `csnMin` (§3.4) ;
   - la fusion du patron Observer en une seule SPI `CardReaderEventHandler` (§4.3) ;
   - l'introspection de la session sécurisée Calypso via `SecureSessionStatus` et `SecureSessionType` (§5.3) ;
   - l'extraction de `RfTechnology` et `CardType` dans une **nouvelle API socle** (`Terminal Definitions API`) (§7.2, §7.6) ;
   - le **modèle builder `CardDetectionSettings`** pour la paramétrisation de la détection (§7.4) ;
   - le **filtre `CardSelector.filterByCardType`** comme critère unique de filtrage typé à la sélection (§7.3) ;
   - l'**exposition du `CardType` détecté sur `CardSelectionResult`** (§7.5).
3. **Le contenu détaillé** des diagrammes UML `3.0.0-SNAPSHOT` des trois APIs existantes (`Reader`, `Card`, `Calypso Card`) et `1.0.0-SNAPSHOT` du nouveau dépôt UML `calypsonet-terminal-definitions-uml-api`, qui matérialisent ces choix — **accessibles directement** via les liens fournis dans la section [Diagrammes UML de référence](#diagrammes-uml-de-référence) en tête de document, sous deux formes : version finale et version avec diff 2.x → 3.0.0.
4. **L'introduction** de la nouvelle API socle (dépôt UML créé, module Java `keypop-definitions-jvm-api` à créer).
5. **Le principe** d'une procédure de migration dédiée fournie ultérieurement aux intégrateurs (cf. §8).

### 9.2 Points d'attention pour la revue

Quelques points pour lesquels une attention particulière du TC est sollicitée :

- la **stabilité du contenu initial** des énumérations `RfTechnology` et `CardType` (§7.2) — l'ajout futur de valeurs sera rétro-compatible, mais le retrait d'une valeur ne le serait pas ; en particulier, le choix de représenter ISO 14443-4 par une seule valeur `ISO_14443_4` sans distinction A/B (cf. §7.2, note sur `CardType`) mérite une confirmation explicite du TC ;
- l'opportunité de la **suppression intégrale** de `ConfigurableCardReader` sans phase de dépréciation transitoire (§7.3), justifiée par le contexte de version majeure ;
- la **sémantique du `csnMin`** comme seuil sur le CSN pour les bornes de durée (§3.3) — ce mécanisme doit pouvoir être exploité par tous les profils de déploiement Calypso ciblés ;
- le **contrat de cycle de vie des `SmartCard`** mémorisées par le `CardReader` (§2.3.2), qui est documenté en Javadoc côté implémentation mais n'apparaît pas dans le diagramme UML ; le TC est invité à confirmer que cette description en prose suffit, ou à demander une formalisation supplémentaire.

### 9.3 Étapes suivantes

Une fois la version 3.0.0 validée par le TC Terminal :

1. **Finalisation des diagrammes UML** : retrait des éléments barrés (`<s>`) et des éléments en gris (`<color:grey>`) qui ne sont pas retenus, génération des SVG définitifs, passage des dépôts de `3.0.0-SNAPSHOT` à `3.0.0` (et de `1.0.0-SNAPSHOT` à `1.0.0` pour le dépôt UML `calypsonet-terminal-definitions-uml-api`, déjà créé).
2. **Création du nouveau module Java** `keypop-definitions-jvm-api` correspondant à la Terminal Definitions API, et **alignement des modules Java Keypop** existants (`keypop-reader-java-api`, `keypop-card-java-api`, `keypop-calypso-card-java-api`) sur la 3.0.0.
3. **Rédaction et publication du guide technique de migration** à destination de l'intégrateur (cf. §8).
4. **Communication** de la disponibilité de la 3.0.0 aux intégrateurs et aux groupes de travail CNA concernés.

---

*Fin du document.*
