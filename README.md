# Projet INF5153 – Bataille navale (Partie 1 : Conception)

## 1. Information générale
- **Branche principale** : `main`
- **Branche de travail** : `dev`
- **Outil UML** : PlantUML
- **Livrables** : diagrammes UML (.puml + .png) et ce README

---

## 2. Objectif de la Partie 1
Réaliser la **conception complète du système** du jeu *Bataille navale* à l’aide des **diagrammes UML** :
- Architecture
- Cas d’utilisation
- Classes
- Séquences 
- Packages
- Composants
- Déploiement

Chaque diagramme doit être versionné et accompagné de son export image.

---

## 3. Règles et portée
- Grille de 10 × 10 cases, 5 navires standards.
- Modes : joueur vs joueur (en ligne) ou joueur vs IA (débutant / avancé).
- Partie 1 : uniquement la **modélisation** (aucun code).
- Partie 2 : implémentation et CI/CD (hors portée ici).

---

## 4. Organisation du dépôt

```plaintext
.
├─ diagrammes/
│  └─ UML/
│     └─ squelettes_diagrammes/
│        ├─ _exports/
│        │  ├─ Diagramme de Composants.png
│        │  ├─ diagramme_architecture_logicielle.png
│        │  ├─ ...
│        ├─ diagramme_architecture_logicielle.puml
│        ├─ diagramme_cas_utilisation.puml
│        ├─ ...
└─ tools/
   └─ plantuml.jar   (téléchargé automatiquement par les scripts)
```

---

## 4.1 Génération automatique des diagrammes PlantUML

Pour éviter de générer manuellement chaque image PNG, deux scripts sont disponibles dans le dossier `tools/`.

### Windows (PowerShell)
Exécuter depuis la racine du projet :
```bash
  powershell -ExecutionPolicy Bypass -File tools/generate-uml.ps1
```
### Linux / macOS (Bash)
Rendre le script exécutable une fois : 
```bash
  chmod +x tools/generate-uml.sh
```
Puis exécuter : 
```bash
  ./tools/generate-uml.sh
```
### Résultat
- Chaque fichier `.puml` trouvé sous `diagrammes/UML/` est automatiquement converti en `.png`.
- Les images générées sont placées dans un sous-dossier `_exports` situé à côté de chaque fichier `.puml`.
- Exemple :
```
diagrammes/UML/squelettes_diagrammes/diagramme_architecture_logicielle.puml
diagrammes/UML/squelettes_diagrammes/_exports/diagramme_architecture_logicielle.png
```

### Prérequis
- **Java** installé et accessible dans le terminal (`java -version`)
- **Graphviz (dot)** recommandé pour une meilleure mise en page (`choco install graphviz` sous Windows)
- **Connexion Internet** requise uniquement lors de la première exécution (le script télécharge automatiquement `plantuml.jar` dans `tools/`)

### Astuce
Les scripts peuvent être relancés à tout moment pour régénérer l’ensemble des diagrammes UML à jour dans leurs dossiers `_exports/`.  

---

## 5. Tableau de suivi des membres

Chaque membre du groupe possède une branche nommée `diag/<prenom>` contenant ses diagrammes.  
Le tableau ci-dessous permet de suivre l’avancement : chaque membre coche (X) les diagrammes inclus dans sa branche.

| Membre  | Branche                              | Architecture | Use Case | Classes | Séquences | Packages | Composants | Déploiements |
|---------|--------------------------------------|-------------|----------|---------|-----------|----------|------------|--------------|
| Mehdi   | `diag/mehdi`                         | X           |          |         |           |          |            | X            |
| Juan    | `juan-ruesga/diagrammes`             |             | X        |         |           |          | X          |              |
| Aki     | `juan-ruesga/diagrammes`, `akii_diag` |             | X        |         |           | X        |            |              |
| Mawuena | `diag_mawuena_classes`               |             |          | X       | X         |          |            |              |
| Souhail | `conception/diag_sou`                |             |          |         | X         |          |            |              |

---

## 6. GRASP – Principes appliqués

Les **patrons GRASP** (General Responsibility Assignment Software Patterns) ont été utilisés pour structurer la conception du système *Bataille Navale* et répartir les responsabilités de manière cohérente entre les couches et objets.
Ils assurent une architecture **claire, maintenable et extensible**, tout en respectant les principes d’**encapsulation** et de **faible couplage**.

| Patron                   | Application dans le projet                                                                                                                                          | Rôle principal                                                                               |
| :----------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------- |
| **Information Expert**   | `Board`, `Jeu` et `ServiceStats` détiennent l’information nécessaire pour exécuter leurs responsabilités (état des grilles, navires, statistiques).                 | Attribuer les responsabilités aux classes qui *savent* déjà les données nécessaires.         |
| **Creator**              | `GameController` et `GestionnairePartieImpl` créent les objets `Joueur`, `Grille`, `Navire` et `Jeu`.                                                               | Centraliser la création dans les classes qui *agrègent* ou *utilisent* les instances créées. |
| **Controller**           | `GameController`, `MatchmakingController` et `ControleurStats` orchestrent les interactions entre l’interface graphique et la logique métier.                       | Servir de *zone tampon* entre la vue et le modèle, en coordonnant les actions.               |
| **Low Coupling**         | Les couches UI, Application, Services métier et Domaine communiquent uniquement via interfaces (`IStatistiques`, `IActionsJeu`, `IStrategiesIA`, etc.).             | Réduire les dépendances et l’impact des changements.                                         |
| **High Cohesion**        | Chaque package a une responsabilité précise : `UI_Client_JavaFX` pour l’affichage, `Services_Metiers` pour la logique du jeu, `Infrastructure` pour la persistance. | Maintenir des modules compréhensibles et spécialisés.                                        |
| **Polymorphism**         | Les IA utilisent le patron *Strategy* (`IA_Strategie`, `IA_Debutant`, `IA_Avancee`) pour varier leur comportement sans conditions explicites.                       | Gérer les variations de comportement par l’envoi de messages, non par des `if/else`.         |
| **Pure Fabrication**     | Les classes `ServiceStats`, `StatsRepository` et `WebSocketGateway` ont été introduites pour déléguer des responsabilités techniques non liées au domaine.          | Créer des classes utilitaires pour maintenir faible couplage et forte cohésion.              |
| **Indirection**          | L’API REST et la couche *Application* assurent la médiation entre UI et services métier, tandis que `WebSocketGateway` déconnecte les composants du temps réel.     | Introduire un intermédiaire pour éviter un couplage direct entre deux objets.                |
| **Protected Variations** | Les interfaces (`IStatistiques`, `IActionsJeu`, `IDAO`) isolent les parties susceptibles de changer (ex. moteur d’IA ou moteur de BD).                              | Encapsuler ce qui varie derrière une interface stable.                                       |

---

### 6.1 Justification des responsabilités selon GRASP

L’application des patrons GRASP s’est traduite dans la conception par une **répartition réfléchie des responsabilités** :

1. **Contrôleurs (Controller)**

    * `GameController` reçoit les commandes de la *VueJeu* et les traduit en actions sur le modèle (`Jeu`, `Grille`, `Joueur`).
    * `MatchmakingController` coordonne la création et la jonction de parties en ligne.
    * `ControleurStats` orchestre la consultation et l’affichage des statistiques.
      → Ces contrôleurs encapsulent la logique d’orchestration, réduisant le couplage entre interface et domaine.


2. **Spécialistes de l’Information (Information Expert)**

    * `Board` connaît la disposition des navires et détermine les résultats des tirs.
    * `ServiceStats` sait comment extraire et calculer les performances d’un joueur.
    * `Jeu` conserve l’état courant (tours, gagnant, tirs restants).
      → Ces classes détiennent les informations nécessaires pour répondre aux requêtes sans dépendances externes.


3. **Création d’objets (Creator)**

    * `GameController` instancie les grilles et les joueurs au lancement d’une partie.
    * `GestionnairePartieImpl` crée les objets `Jeu` en injectant la bonne stratégie IA.
      → La création est confiée aux classes qui possèdent ou utilisent directement les objets à instancier.


4. **Cohésion forte (High Cohesion) & Couplage faible (Low Coupling)**

    * Les dépendances inter-couches sont gérées via des interfaces et des services dédiés.
    * Chaque package du diagramme de *packages* est autonome : UI, Application, Services, Domaine, Infrastructure.
      → La modification d’une couche n’impacte pas les autres.


5. **Polymorphisme et extensibilité (Polymorphism / Protected Variations)**

    * L’IA varie par type (`Débutant`, `Avancée`) grâce au polymorphisme sur l’interface `IStrategiesIA`.
    * Les dépôts (`IDAO`) et services métiers sont définis via des interfaces stables, isolant la base SQLite.
      → De nouvelles stratégies d’IA ou bases de données peuvent être ajoutées sans modifier le code existant.


6. **Fabrication pure & Indirection**

    * `WebSocketGateway` sépare le temps réel de la logique métier.
    * `StatsRepository` et `DepotsSQLite3` encapsulent la persistance.
      → Ces classes n’existent pas dans le domaine, mais améliorent la cohésion et la maintenabilité du système.



> **En résumé**, les patrons GRASP ont permis de structurer le projet *Bataille Navale* autour d’objets responsables, faiblement couplés et hautement cohésifs, garantissant une architecture évolutive et conforme aux bonnes pratiques de conception enseignées.


---

### 7. Technologies et choix d’architecture

| Élément | Technologie choisie | Justification |
|----------|--------------------|----------------|
| **Langage** | Java 21 | Langage objet robuste, bien adapté à la modélisation UML et aux patrons GRASP. |
| **Architecture** | 3-tiers (Client / Serveur / Persistance) + MVC côté client | Séparation claire des responsabilités et facilité d’évolution. |
| **Interface graphique** | JavaFX | Interface moderne et modulaire, facilite la vue du pattern MVC. |
| **Serveur applicatif** | Spring Boot | Simplifie les services REST et WebSocket pour le multijoueur. |
| **Base de données** | SQLite 3 | Conforme à l’énoncé, léger et sans serveur. |
| **Accès aux données** | JDBC / JDBI | Simplicité d’intégration avec SQLite. |
| **Diagrammes UML** | PlantUML | Outil libre, compatible IntelliJ et GitLab CI. |
| **Scripts de génération** | PowerShell / Bash | Automatisation multi-OS pour la génération des PNG. |

> Cette stack technologique respecte les contraintes de l’énoncé et facilite la mise en œuvre de la partie 2 (implémentation).

---

---

## 8. Diagrammes UML du projet

Cette section regroupe l’ensemble des diagrammes UML du projet *Bataille navale*.  
Chaque diagramme est stocké dans le dossier `diagrammes/UML/` et généré automatiquement au format `.png` via les scripts fournis dans la section [4.1](#41-génération-automatique-des-diagrammes-plantuml).

### 8.1 Diagramme d’architecture (réalisé par Mehdi)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_architecture_logicielle.puml`

**Image générée :**

![Diagramme d’architecture](diagrammes/UML/squelettes_diagrammes/_exports/diagramme_architecture_logicielle.png)

> **Description :**
> Ce diagramme illustre l’architecture logicielle du système selon une structure **3-tiers** :
> - **Client (JavaFX)** : interface et contrôleur côté utilisateur (pattern MVC)
> - **Serveur applicatif (Spring Boot)** : logique métier et services (matchmaking, partie, IA, statistiques)
> - **Persistance (SQLite)** : stockage des données via DAO / Repository
>
> Les communications entre le client et le serveur se font via **REST (HTTP/JSON)** et **WebSocket** pour la gestion en temps réel des parties.


### 8.2 Diagramme de cas d'utilisation (réalisé par Juan & Aki)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_cas_utilisation.puml` 

**Image générée :**

![Diagramme de cas d'utilisation](diagrammes/UML/squelettes_diagrammes/_exports/diagramme_cas_utilisation.png)

> **Description :**
> Ce diagramme présente les principaux cas d’utilisation du système *Bataille Navale*, en se concentrant sur les interactions entre les acteurs externes (Joueur, Spectateur) et le système.
>
> Le Joueur est l’acteur principal : il peut jouer une partie, choisir un mode de jeu, placer ses navires, effectuer un tir et consulter diverses statistiques.
> Le Spectateur est un acteur secondaire optionnel qui peut observer une partie en cours sans y participer.
>
> Le diagramme met en évidence :
>
> * Des relations d’inclusion (`<<include>>`) pour représenter les étapes obligatoires du scénario *Jouer une partie*, notamment *Choisir le mode de jeu*, *Placer les navires* et *Effectuer un tir*.
> * Des relations d’extension (`<<extend>>`) pour différencier les deux modes possibles du jeu (*Jouer contre l’IA* et *Jouer contre un humain en ligne*) à partir du cas *Choisir le mode de jeu*.
> * Deux cas d’utilisation spécifiques dédiés aux statistiques accessibles directement par le Joueur : *Consulter les statistiques personnelles* et *Consulter le classement des joueurs*.
>
> Ce modèle ne contient que les cas d’utilisation effectivement initiés par les acteurs et exclut volontairement la logique interne du système (traitement d’un tir, détermination du vainqueur, IA), conformément aux bonnes pratiques UML.


---

> **Corrections faites suite à la réunion avec Serge :**
>
> 1. Suppression des acteurs internes :
>
>    * Retrait du *Serveur du jeu*, considéré comme faisant partie du système et non comme un acteur externe.
>    * Retrait du *Joueur IA*, qui représente une logique interne et non une interaction utilisateur.
>
> 2. Suppression des cas d’utilisation internes ou techniques :
>
>    * Les cas *Traiter le résultat du tir* et *Déterminer le vainqueur/perdant* ont été retirés, car ils correspondent à des opérations internes du système et non à des actions initiées par un utilisateur.
>
> 3. Réorganisation de la section Statistiques :
> 
>    * Le cas général *Gérer les Statistiques* a été supprimé.
>    * Il a été remplacé par deux cas d’utilisation directement déclenchés par le Joueur :
>
>        * *Consulter les statistiques personnelles*
>        * *Consulter le classement des joueurs*
>
> 4. Clarification des relations include/extend :
>
>    * *Effectuer un tir* a été ajouté comme `<<include>>` de *Jouer une partie*, puisqu’il fait obligatoirement partie du déroulement du jeu.
>    * Les relations `<<extend>>` superflues ont été retirées afin d’éviter de représenter des variantes non pertinentes.
>
> 5. Recentrement du diagramme sur les actions réelles des acteurs :
>
>    * Toutes les opérations internes du système ont été retirées pour respecter le principe fondamental d’UML selon lequel un cas d’utilisation doit représenter une intention utilisateur et non un traitement interne.


### 8.3 Diagrammes de classe (réalisé par Mawuena)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_classes_domaine.puml`

**Image générée :**

![Diagramme de classes](diagrammes/UML/squelettes_diagrammes/_exports/Diagramme_de_classes.png)

> **Description :**
> Ce diagramme de classes représente la **structure complète du jeu *Bataille Navale*** organisée en quatre couches : **UI**, **Domaine**, **Services métiers** et **Infrastructure**. Il modélise le noyau logique du jeu (grille, navires, joueurs, coups), les services métier (orchestration du jeu, règles, stratégies IA, statistiques) et la persistance des données via SQLite. L'architecture applique les principes GRASP pour assurer un faible couplage entre les couches et une forte cohésion au sein de chaque module.

> **Corrections apportées :**
>
> Un lien d'association a été ajouté entre la classe `Coup` et la classe `Case` car le statut d'une case est modifié par un coup, établissant ainsi un lien entre les deux. De plus, l'association entre `Coup` et l'enum `Reponse` a également été ajoutée.


### 8.4 Diagramme de packages (réalisé par Akii)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_packages.puml`

**Image générée :**

![Diagramme de packages](diagrammes/UML/squelettes_diagrammes/_exports/diagramme_packages.png)

> **Description :**
> Ce diagramme illustre l’**architecture en couches** du projet *Bataille navale*, en représentant la répartition logique des responsabilités entre les différents packages.
>
> * **UI_Client_JavaFX** : contient l’interface graphique, le contrôleur du jeu et les effets visuels (pattern MVC).
> * **Application** : assure la communication entre le client et les services via l’API REST et WebSocket.
> * **Services_Métiers** : regroupe la logique du jeu (actions, IA, statistiques) et définit les interfaces métier (`IActionsJeu`, `IStrategiesIA`, `IStatistiques`, `IDAO`).
> * **Domaine** : contient les entités principales (Partie, Joueur, Grille, Navire, Coup) et les règles du jeu.
> * **Infrastructure** : gère la persistance des données via SQLite3 et implémente les dépôts (`DepotsSQLite3`, `BaseDeDonnees_SQLite3`).
> * **Services_Externes** : représente le serveur distant utilisé pour les parties multijoueurs en ligne.
>
> Le diagramme met en avant :
>
> * Une dépendance descendante entre les couches (UI → Application → Services → Domaine → Infrastructure).
> * L’application du **principe DIP (Dependency Inversion Principle)** : les interfaces sont définies dans la couche métier et implémentées dans la couche infrastructure.
> * Une séparation claire entre la logique de présentation, la logique métier et la persistance des données, garantissant une **architecture modulaire et maintenable**.


### 8.5 Diagramme de composants (réalisé par Juan)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_composants.puml`

**Image générée :**

![Diagramme de composants](diagrammes/UML/squelettes_diagrammes/_exports/Diagramme de Composants.png)

> **Description :**
> Ce diagramme de composants illustre la **structure logicielle détaillée** du système *Bataille navale* et les interactions entre les principaux modules.
>
> * **Infrastructure** : gère la persistance avec la base de données SQLite3 et les dépôts spécialisés (`Dépôt Parties`, `Dépôt Joueurs`, `Dépôt Statistiques`).
> * **UI_Client_JavaFX** : comprend la vue graphique (`Vue_UI`), le contrôleur client (`Contrôleur_Client`) et les effets visuels (`Effets Spéciaux`).
> * **Application** : centralise les communications via l’**API REST / WebSocket**, reliant le client aux services du serveur.
> * **Services_Métiers** : contient la logique fonctionnelle du jeu (`GameService`, `StatsService`, `IAServices`) et implémente les interfaces métiers.
> * **Domaines** : regroupe les entités du cœur du jeu (Partie, Joueur, Navire, Grille, Coup).
> * **Services_Externes** : représente le serveur distant qui héberge les parties multijoueurs.
>
> Le diagramme met en évidence :
>
> * L’usage d’**interfaces** pour découpler les composants (`IEffetsSpeciaux`, `IStatistiques`, `IActionsJeu`, `IStrategiesIA`, `IDAO`).
> * Des **dépendances hiérarchiques claires** entre les couches : la vue interagit avec le contrôleur, qui communique avec l’API, puis les services, jusqu’à la base de données.
> * L’application des **principes SOLID**, notamment le **DIP (Dependency Inversion Principle)**, garantissant une conception modulaire, réutilisable et facilement testable.

> **Corrections apportées :**
> Les ports d'entrée et de sortie ont été intégrés au diagramme de composants pour clarifier les dépendances et les interactions entre chaque composant et ses interfaces associées.

### 8.6 Diagrammes de séquences

### 8.6.1 Diagramme de séquence 1 : Jouer un tir (réalisé par Mawuena)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_sequence_tir.puml`

**Image générée :**

![Diagramme de séquence - Jouer un tir](diagrammes/UML/squelettes_diagrammes/_exports/diagramme_sequence_tir.png)

> **Description :**
> Ce diagramme de séquence illustre le **flux complet d'un tir** dans le jeu *Bataille Navale*. Il montre comment le `Jeu` orchestre l'interaction entre la `Grille`, les `Navire` et les `Etat` pour traiter un tir. Le joueur lance un tir en spécifiant une position ; la `Grille` (Expert en Information) marque l'état de la position comme touchée, cherche le navire occupant cette position, notifie le navire de la touche, puis détermine le résultat final (EAU, TOUCHE ou COULE). Ce flux démontre le faible couplage entre `Navire` et `Etat` : la `Grille` gère exclusivement leur orchestration, chaque entité conservant une responsabilité unique.


### 8.6.2 Diagramme de séquence 2 : Créer / Rejoindre une partie (réalisé par Souhail)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_sequence_creer_rejoindre_partie.puml`

**Image générée :**

![Diagramme de séquence - Créer/Rejoindre une partie](diagrammes/UML/squelettes_diagrammes/_exports/diagramme_sequence_cree_rejoindre_partie.png)

> **Description :**
> Ce diagramme de séquence illustre le **processus complet de création ou de rejoindre une partie**, que ce soit contre une **IA** ou un **adversaire humain en ligne**. Le diagramme montre comment la `VueJeu` déclenche les actions côté client, relayées ensuite par le `ClientController` vers le serveur. Selon le mode choisi, le `MatchmakingController` délègue soit la création immédiate d’une partie IA au `GameService`, soit l’inscription dans la file d’attente gérée par le `MatchmakingService` pour une partie en ligne.
>
> Lorsque deux joueurs humains sont appariés, le `GameService` crée la partie et la sauvegarde via `StockageJeu`, puis le `WebSocketGateway` notifie les joueurs en temps réel. Dans les deux modes, le flux se termine lorsque le `ClientController` reçoit les informations initiales de la partie et les transmet à la `VueJeu` pour afficher le plateau de début de jeu.
>
> Ce scénario met en évidence une **séparation claire des responsabilités** : le client se limite aux interactions utilisateur, le contrôleur serveur gère le routage des requêtes, les services métiers prennent en charge la logique de création de partie, et la couche de persistance assure l’enregistrement. Le système reste ainsi **faiblement couplé**, cohérent avec l’architecture en couches et les principes GRASP appliqués.



### 8.6.3 Diagramme de séquence 3 : Consulter statistiques (réalisé par Mehdi)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_sequence_stats.puml`

**Image générée :**

![Diagramme de séquence - Consulter statistiques](diagrammes/UML/squelettes_diagrammes/_exports/diagramme_sequence_stats.png)

> **Description :**
> Ce diagramme illustre le **scénario “Consulter les statistiques”** d’un joueur, depuis la demande dans l’interface jusqu’à l’affichage du résumé des performances.
>
> **Participants clés :** *GUI*, *ControleurStats*, *ServiceStats*, *StockageStats* (base de données).
>
> **Flux principal :**
>
> 1. **Demande utilisateur** : le *Joueur* choisit l’option “Mes statistiques” dans le menu.
> 2. **Appel contrôleur** : la *GUI* envoie la requête à *ControleurStats* via `afficherMesStats(userId)`.
> 3. **Récupération métier** : *ControleurStats* délègue la requête à *ServiceStats*, responsable de la logique de calcul et d’agrégation.
> 4. **Accès à la persistance** : *ServiceStats* interroge *StockageStats* pour charger les statistiques du joueur.
> 5. **Résultat** :
>
>    * Si les statistiques existent → retour des données agrégées (victoires, défaites, ratio de précision, etc.).
>    * Sinon → création et retour d’un objet de statistiques vides.
> 6. **Affichage** : *ControleurStats* retourne un résumé au *GUI*, qui met à jour l’écran avec les valeurs correspondantes.
>
> **Points saillants :**
>
> * Séparation claire entre la **présentation**, la **logique métier** et la **persistance**.
> * Gestion des cas sans données existantes (création de statistiques par défaut).
> * Favorise la **responsabilité unique** (GRASP : *Information Expert* dans *ServiceStats*) et la **faible dépendance** entre couches.


### 8.6.3 Diagramme de séquence 4 : Initialisation de la grille (réalisé par Mehdi)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_sequence_initialisation_grille.puml`

**Image générée :**

![Diagramme de séquence - Initialisation de la grille](diagrammes/UML/squelettes_diagrammes/_exports/diagramme_sequence_initialisation_grille.png)

> **Description :**
> Ce diagramme montre l'initialisation de la grille au lancement d'une partie. Le jeu demande à la grille de se créer avec une taille donnée. La grille instancie toutes les positions possibles et leur associe un état (non touchée par défaut), puis prépare la liste vide de navires. Chaque case de la grille est prête à accueillir les navires et à recevoir des tirs.


### 8.6.4 Diagramme de séquence 5 : Observation d'une partie (réalisé par Mehdi)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_sequence_observer_partie.puml`

**Image générée :**

![Diagramme de séquence - Observation d'une partie](diagrammes/UML/squelettes_diagrammes/_exports/diagramme_sequence_observer_partie.png)

> **Description :**
> Ce diagramme montre comment un spectateur observe une partie en cours. Le spectateur interroge l’objet `Jeu`, qui récupère l’état actuel de la grille, la liste des navires et les informations des joueurs, puis transmet l’ensemble au spectateur. Le spectateur dispose ainsi de l’état complet et synchronisé de la partie en lecture seule.


### 8.7 Diagramme de déploiement (réalisé par Mehdi)
**Fichier source :** `diagrammes/UML/squelettes_diagrammes/diagramme_deploiement.puml`

**Image générée :**

![Diagramme de déploiement](diagrammes/UML/squelettes_diagrammes/_exports/diagramme_deploiement.png)

>**Description :**
>
> Ce diagramme illustre le déploiement physique de l'application Bataille Navale dans un environnement client-serveur centralisé supportant deux modes de jeu : Humain vs Humain et Humain vs IA.
>
> **Poste Joueur (JavaFX) :**
> Les utilisateurs lancent une application desktop JavaFX embarquant l'interface graphique, le contrôleur et un moteur de jeu local. Cette application communique avec le serveur central via HTTP/JSON (REST) et WebSocket (temps réel) pour synchroniser l'état de la partie. Une base de données SQLite locale stocke les données nécessaires au client via JDBC.
>
> **Poste Spectateur :**
> Un client WebSocket dédié permet aux spectateurs de suivre les parties en temps réel via une interface de visualisation, sans participer au jeu.
>
> **Serveur Multijoueur :**
> Un serveur central Spring Boot gère l'ensemble de la logique métier, le matchmaking entre joueurs, et coordonne les deux modes de jeu. Le Service MatchMaking assure l'appairage des joueurs pour les parties en Humain vs Humain. Le Service IA intervient uniquement en mode Humain vs IA pour gérer les stratégies et décisions de l'intelligence artificielle.
>
> **Persistance des données :**
> La base de données SQLite Server est centralisée et accessible exclusivement par le serveur applicatif via JDBC, garantissant la cohérence des données et la centralisation des traitements métiers.

## 9. Check-list avant remise
- [X] Tous les diagrammes UML présents (.puml + .png)
- [X] GRASP justifié dans le README
- [X] Arborescence propre et cohérente
- [X] README complété
- [X] Accès GitLab configuré pour l’enseignant


