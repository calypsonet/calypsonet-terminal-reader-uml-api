# Changements apportés par la version 3.0.0 des APIs Terminaux

> Périmètre : **Reader API**, **Card API** et **Calypso Card API**
> Source : diagrammes UML de référence (`3.0.0-SNAPSHOT/api_class_diagram.puml` de chaque module)
>
> **Conventions de lecture** :
> - Les éléments **barrés** (`<s>`) dans les diagrammes 3.0.0 ne sont conservés que pour faciliter la lecture du delta entre la 2.x et la 3.0.0. Ils seront **absents** de la version finale 3.0.0.
> - Les éléments en **bleu** sont les **ajouts** de la 3.0.0.
> - Les éléments en **gris** (`<color:grey>`) sont des éléments **en cours d'étude** (« work in progress ») qui n'avaient jamais été implémentés dans Keypop Java. La 3.0.0 fait le ménage : ces éléments sont retirés des diagrammes finaux (ou seront marqués comme tels pour la version finale). Ils sont mentionnés ici quand cela aide à comprendre la trajectoire d'une notion (par exemple le retrait du `MultichannelCardSelector` expérimental au profit du nouveau modèle multicanal porté par le `CardSelectionManager`).
> - Les relations UML notées `+-` (composition par agrégation) indiquent que l'élément pointé est une **classe interne** (`inner class` / type imbriqué) du conteneur — par exemple `CardReaderEvent.Type` est une énumération interne de `CardReaderEvent`, et `IsoCardSelector.FileOccurrence` / `IsoCardSelector.FileControlInformation` sont des énumérations internes d'`IsoCardSelector`. Cette convention conditionne les imports et la nomenclature Java.

---

## Table des matières

1. [Vue d'ensemble](#1-vue-densemble)
2. [Thème 1 — Support des canaux logiques multiples](#2-thème-1--support-des-canaux-logiques-multiples)
3. [Thème 2 — Contre-mesure de la faille de sécurité par attaque relai](#3-thème-2--contre-mesure-de-la-faille-de-sécurité-par-attaque-relai)
4. [Thème 3 — Simplification de la gestion de l'observation](#4-thème-3--simplification-de-la-gestion-de-lobservation)
5. [Thème 4 — Connaissance de l'état courant de la session sécurisée](#5-thème-4--connaissance-de-létat-courant-de-la-session-sécurisée)
6. [Thème 5 — Améliorations sémantiques (renommages et migration de concepts)](#6-thème-5--améliorations-sémantiques-renommages-et-migration-de-concepts)
7. [Impacts sur les implémentations Keypop (Java)](#7-impacts-sur-les-implémentations-keypop-java)

---

## 1. Vue d'ensemble

La version 3.0.0 est une **version majeure** : elle introduit des ruptures de compatibilité sur les trois APIs. Les changements sont motivés par cinq grands chantiers :

| # | Thème | Reader | Card | Calypso Card |
|---|---|:---:|:---:|:---:|
| 1 | Canaux logiques multiples | ● | ● | ● |
| 2 | Contre-mesure attaque relai | — | ● | ● |
| 3 | Simplification observation | ● | — | — |
| 4 | État courant de la session sécurisée | — | — | ● |
| 5 | Améliorations sémantiques | ● | ● | ● |

Les conséquences transverses :

- **Suppression de tout l'héritage déprécié** : la version majeure 3.0.0 est l'occasion de faire le ménage. Tous les éléments qui avaient été marqués `@Deprecated` dans les versions 1.x ou 2.x — y compris ceux qui n'avaient été dépréciés que tardivement — sont supprimés sans alternative de compatibilité. Plus aucun élément déprécié ne subsiste dans la 3.0.0.
- **Suppression des éléments « work in progress »** (en gris, `<color:grey>`) qui n'avaient **jamais été implémentés dans Keypop Java**. Ils figuraient dans les diagrammes uniquement à titre prospectif. Sont concernés :
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

Le déclencheur concret de ce chantier est l'arrivée d'un nouveau produit, **OpenSAM**, dont le modèle d'usage repose précisément sur la cohabitation de plusieurs applications sélectionnées en parallèle sur des canaux logiques distincts. La 3.0.0 fournit l'infrastructure d'API nécessaire pour pouvoir l'intégrer proprement dans Keypop, sans bricolage côté implémentation.

### 2.2 Reader API

#### Sélection multi-canal

- **Nouvelle méthode** `CardSelectionManager.processMultichannelCardSelectionScenario(CardReader, ChannelSelectionPolicy)` — équivalent multicanal de `processCardSelectionScenario`.
- **Nouvelle énumération** `ChannelSelectionPolicy` :
  - `ALLOW_BASIC_CHANNEL` — autorise l'usage du canal de base (canal 0) en plus des canaux logiques additionnels ;
  - `LOGICAL_CHANNEL_ONLY` — restreint la sélection aux canaux logiques supplémentaires.

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

#### 2.3.1 Rôle du `SmartCardSpi` transmis au `ProxyReaderApi`

Le passage du `SmartCardSpi` dans `transmitCardRequest(...)`, `transmitCardRequestAndCloseChannel(...)` et `closeChannel(...)` n'est **pas** un simple véhicule pour le numéro de canal. Il joue trois rôles cumulatifs :

1. **Transporter le numéro de canal logique** (via `MultichannelSmartCardSpi.getChannel()`) sur lequel doit être adressée la requête APDU ou la fermeture.
2. **Transporter l'état actif/non-actif de la SmartCard** afin que le `ProxyReaderApi` puisse **vérifier en amont** que la carte est encore active avant d'émettre toute requête. Cela protège l'application contre l'usage involontaire d'une SmartCard dont l'état réel ne correspond plus à ce que pense le code appelant (carte retirée, canal fermé par une autre voie, exception de communication antérieure, etc.).
3. **Permettre au `ProxyReaderApi` de désactiver la SmartCard** (`SmartCardSpi.deactivate()`) si la situation l'exige — typiquement après une exception de communication ou à la fermeture explicite du canal — pour que cette désactivation soit immédiatement visible côté application via `SmartCard.isActive() == false`.

#### 2.3.2 Cycle de vie des `SmartCard` mémorisées par le `CardReader`

Pour que ce contrat d'« état réel reflété » fonctionne, le `CardReader` **mémorise temporairement les références vers les `SmartCard` issues de la dernière sélection** (jusqu'à la prochaine étape qui invalide leur état). Cette mémorisation permet au `CardReader` de **désactiver automatiquement** les SmartCards concernées dans les quatre cas suivants :

1. lors d'une **nouvelle sélection en mode mono-canal** (les anciennes SmartCards deviennent obsolètes) ;
2. lors d'une **demande explicite de fermeture du canal** (`closeChannel`, `transmitCardRequestAndCloseChannel`, `processCommandsAndCloseChannel`) ;
3. lors de l'appel à **`ObservableCardReader.endCardProcessing()`** (fin du traitement de la carte courante) ;
4. lors d'une **exception indiquant que la carte n'est plus contactable** (typiquement `CardCommunicationException` / `ReaderCommunicationException` au niveau Reader, ou `CardBrokenCommunicationException` / `ReaderBrokenCommunicationException` au niveau Card API).

L'objectif est que, dans tous ces cas, **toute tentative ultérieure d'utiliser une SmartCard désormais invalide dans une transaction** (Calypso ou autre) puisse être interceptée par le `ProxyReaderApi` au moment de la vérification de `isActive()` — au lieu de produire une erreur plus tardive et plus difficile à diagnostiquer.

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

### 3.2 Card API

- **Côté requête** : `ApduRequestSpi.getApduExchangeMaxDuration() : Long` — durée maximale tolérée pour l'échange APDU (en millisecondes). Le type retourné est volontairement `Long` (boxed) : la valeur `null` signifie **« pas de borne définie »** pour cette requête.
- **Côté réponse** : `ApduResponseApi.getApduExchangeDuration() : Long` — durée effective de l'échange, telle que mesurée par le lecteur. La valeur `null` signifie **« durée non mesurée »** (le lecteur ne fournit pas la mesure).
- **Nouvelle exception** `ApduExchangeDurationExceededException extends AbstractApduException` — levée par `ProxyReaderApi.transmitCardRequest(...)` lorsqu'une durée maximale a été dépassée.

### 3.3 Calypso Card API

Toutes les nouvelles méthodes prennent un paramètre `csnMin` qui est un **seuil** sur le CSN (Card Serial Number) : la règle s'applique à **toute carte dont le CSN est supérieur ou égal à `csnMin`**. Ce mécanisme permet d'introduire ou de durcir progressivement les bornes de durée au fur et à mesure que de nouvelles générations de cartes (au CSN plus élevé) sont émises, sans pénaliser les cartes plus anciennes.

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

- `startCardDetection(DetectionMode detectionMode, CardReaderEventHandler eventHandler)` — l'enregistrement de l'observateur (et sa déclaration) se fait désormais **au moment où la détection démarre**, en une seule opération.

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

- **Nouvelle méthode** `TransactionManager.getSecureSessionStatus() : SecureSessionStatus` — accesseur de l'état courant.
- **Nouvelle interface** `SecureSessionStatus` :
  - `boolean isOpen()` — une session sécurisée est-elle actuellement ouverte ?
  - `SecureSessionType getType()` — nature cryptographique de la session ;
  - `WriteAccessLevel getWriteAccessLevel()` — niveau d'écriture demandé à l'ouverture. Cette méthode retourne **`null` en mode PKI** (`SecureSessionType.ASYMMETRIC`), où l'ouverture de session se fait via `prepareOpenSecureSession()` sans `WriteAccessLevel`.
- **Nouvelle énumération** `SecureSessionType` :
  - `SYMMETRIC` (sessions PSO/SAM, modes Regular/Extended) ;
  - `ASYMMETRIC` (mode PKI).

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
- **Suppression** de l'interface expérimentale `MultichannelCardSelector` (2.1.0) — la notion de multi-canal est désormais portée par le `CardSelectionManager` (cf. thème 1) et non plus par un sélecteur dédié.
- `IsoCardSelector` étend désormais directement `CardSelector` et porte les méthodes `filterByDfName`, `setFileOccurrence`, `setFileControlInformation`.

#### Disparitions liées au modèle de canal

- **Supprimée** : `CardSelectionManager.prepareReleaseChannel()`.
- **Supprimée** : `ChannelControl` (énumération) — cf. thème 1.
- **Supprimée** : `CardTransactionManager.processCommands(ChannelControl)`, remplacée par `processCommands()` sans paramètre.

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

#### Renommages indirects

- L'interface `CardTransactionManager` (Reader API) reste référencée par stéréotype sur `TransactionManager` (Calypso) ; la nouvelle interface multicanal `MultichannelCardTransactionManager` est elle aussi référencée par stéréotype sur le nouveau `MultichannelTransactionManager` Calypso.

---

## 7. Impacts sur les implémentations Keypop (Java)

À ce stade, seuls les **diagrammes UML** des trois APIs ont été mis à jour pour 3.0.0. Les modules Java associés (`keypop-reader-java-api`, `keypop-card-java-api`, `keypop-calypso-card-java-api`) doivent encore être alignés. Les chantiers à mener côté Java sont, par module :

### `keypop-reader-java-api`

- Supprimer :
  - `ChannelControl.java` ;
  - `selection/InvalidCardResponseException.java` (doublon de la racine) ;
  - `selection/CommonIsoCardSelector.java` ;
  - les méthodes obsolètes de `ObservableCardReader` (cf. §4.2) et la SPI `spi/CardReaderObserverSpi`, `spi/CardReaderObservationExceptionHandlerSpi`.
- Renommer / refondre :
  - `DetectionMode.SINGLESHOT` → `SINGLE_SHOT` ;
  - `CardReaderEvent.Type.UNAVAILABLE` → `READER_UNREGISTERED` ;
  - `ObservableCardReader.finalizeCardProcessing` → `endCardProcessing` ;
  - déplacer/renommer `NotificationMode` → `selection/CardPresenceNotificationPolicy`.
- Créer :
  - `spi/CardReaderEventHandler` ;
  - `selection/SelectionExecutionPolicy`, `selection/ChannelSelectionPolicy` ;
  - `transaction/spi/MultichannelCardTransactionManager` ;
  - méthodes `processMultichannelCardSelectionScenario`, `getActiveSelectionIndexes`, `isActive`, `isBasicChannel`.
- Mettre à jour la signature des deux `process*` / `schedule*` de `CardSelectionManager`.
- Rédiger la **Javadoc** complète pour chaque ajout/renommage en respectant les conventions du module.

### `keypop-card-java-api`

- Supprimer : `ChannelControl.java`, `ProxyReaderApi#releaseChannel`, `CardResponseApi#isLogicalChannelOpen`.
- Ajouter :
  - `spi/MultichannelSmartCardSpi` ;
  - méthodes de relai-fighting : `ApduRequestSpi#getApduExchangeMaxDuration`, `ApduResponseApi#getApduExchangeDuration` ;
  - `ApduExchangeDurationExceededException` (étend `AbstractApduException`) ;
  - méthode `SmartCardSpi#deactivate` ;
  - méthode `CardSelectionResponseApi#getChannel` ;
  - les trois nouvelles signatures de `ProxyReaderApi` (cf. §2.3).

### `keypop-calypso-card-java-api`

- Supprimer : `processCommands(ChannelControl)` sur `TransactionManager`, exceptions `UnexpectedCommandStatusException`, `ReaderIOException`, `CardIOException`.
- Ajouter :
  - `transaction/SecureSessionStatus` + `transaction/SecureSessionType` ;
  - `transaction/MultichannelTransactionManager` ;
  - méthodes `TransactionManager#getSecureSessionStatus`, `TransactionManager#getLogicalChannelSupport` ;
  - méthodes `assignOpenSecureSessionMaxDuration(...)` et `assignSvOperationMaxDuration(...)` sur les deux interfaces de `*CryptoSecuritySetting`.
- Compléter la **Javadoc** (motivation attaque relai, sémantique du `csnMin`, contrat de `SecureSessionStatus`, etc.).

> **Recommandation** : traiter les trois modules dans l'ordre `card` → `reader` → `calypso-card`, car les dépendances Javadoc et les références croisées (`<<…>>` dans les diagrammes Calypso) suivent cet ordre.
