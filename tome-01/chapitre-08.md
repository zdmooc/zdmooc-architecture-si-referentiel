# Chapitre 8 — DDD : du domaine métier à la conception du logiciel

## 1. Le logiciel exprime toujours une compréhension du métier

Dans les chapitres précédents, nous avons rencontré des paiements acceptés, soumis, exécutés et parfois sans résultat connu. Ces termes semblent familiers. Pourtant, ils ne décrivent pas nécessairement la même chose pour le client, le système de suivi et le partenaire d’exécution.

Si cette ambiguïté entre dans le logiciel, elle ne disparaît pas. Elle devient une condition dans un traitement, une valeur dans une table ou une traduction approximative dans une API. Le système peut alors être techniquement stable tout en produisant une réponse métier trompeuse.

Le Domain-Driven Design, ou DDD, cherche à construire une compréhension du domaine qui guide effectivement la conception. Dans son DDD Reference, Eric Evans met en avant le cœur de domaine, l’exploration collaborative des modèles et un langage partagé dans un contexte explicitement délimité. [1]

DDD n’est donc pas une manière élégante de nommer des classes. Il relie l’apprentissage du métier, les décisions de modélisation et leur expression dans le logiciel.

## 2. Domaine, modèle et logiciel

Le domaine est le champ de connaissance ou d’activité auquel le logiciel s’applique. Le modèle est une sélection d’abstractions utiles pour résoudre des problèmes dans ce domaine. Il ne cherche pas à reproduire toute la réalité. [1]

Dans notre cas fictif, le modèle de suivi doit expliquer les résultats connus et les situations à résoudre. Il n’a pas nécessairement besoin de représenter toutes les opérations internes du partenaire. Inversement, ignorer la différence entre réception et exécution le rendrait insuffisant.

Un modèle peut être simple tout en étant riche de sens. Trois concepts correctement définis peuvent mieux expliquer le problème qu’un diagramme contenant cinquante classes sans responsabilités claires.

La question est de savoir si le modèle permet de raisonner sur les cas d’usage, les règles et les exceptions. Une table peut contribuer à sa réalisation, mais elle ne constitue pas automatiquement le modèle métier. Une représentation conçue pour l’affichage ne suffit pas non plus à définir les règles de changement d’état.

## 3. Une collaboration qui transforme les hypothèses

L’expert métier apporte des situations, des règles et des distinctions. Le développeur apporte la capacité à rendre ces distinctions explicites et exécutables. L’architecte contribue aux frontières et à la cohérence d’ensemble. Le modèle se construit dans leurs échanges, pas dans une transmission unique de spécifications.

Eric Evans insiste sur le lien entre les personnes qui modélisent et celles qui touchent au code. Une séparation trop forte prive les modèles des contraintes de réalisation et prive les développeurs de leur compréhension métier. [1]

Dans notre exemple, le métier dit : « Une demande non confirmée doit être recherchée. » L’équipe demande alors ce que signifie non confirmée, quelle référence permet la recherche et quel résultat autorise une transition. Cette précision peut révéler plusieurs situations que le système traitait jusque-là comme une erreur unique.

Le désaccord n’est pas toujours un obstacle. Il peut indiquer qu’un concept mélange des responsabilités ou que plusieurs modèles légitimes coexistent.

## 4. Construire le langage partagé

Le langage partagé, souvent désigné par ubiquitous language, est structuré autour du modèle et utilisé dans les échanges de l’équipe comme dans le code. Il s’applique à un contexte délimité, pas nécessairement à toute l’entreprise. [1]

Un glossaire est un support, mais il ne suffit pas. Les mots doivent fonctionner dans les scénarios, les tests, les interfaces et les discussions. Si le métier parle de confirmation tandis que le code utilise une méthode `setStatus(4)`, le lien reste faible.

| Terme du cas fictif | Sens proposé à discuter |
|---|---|
| Demande reçue | La demande a été prise en charge selon le contrat local |
| Demande soumise | Une tentative d’envoi a atteint le jalon défini par le protocole |
| Exécution confirmée | Un élément reconnu par le contrat établit l’exécution |
| Résultat inconnu | Le système ne dispose pas des éléments suffisants pour conclure |
| Rejet établi | Un résultat reconnu établit le refus dans le périmètre considéré |

Ces définitions sont des propositions de notre exemple. Elles ne constituent pas un vocabulaire bancaire universel. Même « soumise » demande une précision : l’envoi local, la réception distante et l’acceptation métier ne sont pas identiques.

Renommer un terme peut révéler un changement de modèle. Il faut alors examiner le code et les contrats concernés, plutôt que corriger seulement le document. [1]

## 5. Distinguer conception stratégique et tactique

La conception stratégique examine les modèles, leurs frontières, leurs relations et les parties du domaine dans lesquelles concentrer l’effort. La conception tactique organise les éléments du modèle à l’intérieur de ces frontières. Les deux dimensions se complètent. [1]

Pour notre étude, la stratégie demande si suivi du paiement, comptabilité et notification relèvent du même modèle. La tactique demande ensuite comment représenter l’identité d’un paiement, son montant et les règles de transition.

Commencer directement par des repositories et des classes de base peut donner une apparence DDD à un découpage mal compris. À l’inverse, une carte de contextes sans effet sur les contrats et le code reste une intention.

Ce chapitre établit leur articulation générale. Le deuxième tome approfondira les décisions stratégiques et les mécanismes tactiques de réalisation.

## 6. Concentrer l’effort sur le cœur de domaine

Le cœur de domaine est la partie où une compréhension et une conception particulières apportent une valeur stratégique. Les sous-domaines génériques répondent à des besoins pouvant être satisfaits par des approches plus communes. Le DDD Reference encourage à distinguer le cœur et à concentrer l’effort de modélisation là où il compte. [1]

Cette distinction ne signifie pas que le reste est sans importance. Une fonction générique peut être critique pour la disponibilité ou la sécurité. La valeur stratégique et la criticité opérationnelle ne sont pas la même dimension.

Dans notre organisation fictive, le traitement des cas incertains pourrait constituer un domaine dans lequel l’organisation cherche à se différencier. Cette hypothèse doit être établie avec le métier. Le fait qu’il s’agisse d’un paiement ne suffit pas à classer automatiquement toutes ses fonctions comme cœur de domaine.

L’objectif est d’éviter une sophistication uniforme. Une fonctionnalité simple de consultation administrative peut nécessiter une solution plus directe qu’un traitement riche en règles et exceptions. La documentation Microsoft recommande également de réserver les approches DDD riches aux responsabilités comportant une complexité métier significative. [2]

## 7. Délimiter les contextes

Un bounded context délimite le périmètre où un modèle particulier est défini et applicable. Les limites doivent être explicites et la cohérence du modèle entretenue à l’intérieur. [1]

Le modèle de paiement suit une demande et les faits connus sur son exécution. Un modèle comptable peut raisonner sur des écritures et des périodes. Le modèle de notification raisonne sur un destinataire, un canal et des tentatives de livraison.

```text
Contexte Paiements
  Demande, référence, résultat connu
          │
          ├── contrat vers Comptabilité
          └── contrat vers Notifications

Contexte Notifications
  Destinataire, message, livraison
```

Ce schéma propose des frontières pour notre exercice ; il ne démontre pas qu’elles sont adaptées à tous les cas. Il faut les confronter aux usages, aux responsabilités et aux dépendances.

Le contexte n’est pas une simple catégorie dans une présentation. Il doit avoir une expression dans les responsabilités et le logiciel. Il ne correspond pas pour autant automatiquement à une application déployée indépendamment.

## 8. Ne pas confondre sous-domaine, contexte et microservice

Un sous-domaine représente une partie du problème métier. Un contexte délimité organise l’application d’un modèle dans la solution. Un microservice ajoute un choix d’organisation et de déploiement distribué. Le DDD Reference définit le contexte à travers la portée du modèle et ses manifestations dans le système, non par l’obligation d’un processus distant. [1]

Une application modulaire peut donc contenir plusieurs modèles explicitement séparés. Dans une architecture de microservices, une frontière de contexte peut aider à concevoir une frontière de service. Mais le passage à la distribution doit être justifié séparément.

La documentation Microsoft, qui présente précisément une réalisation en microservices, note également qu’un contexte peut être composé de plusieurs services physiques et que des échanges excessifs invitent à réexaminer les frontières. [2]

Découper chaque entité en service risquerait de transformer une règle cohérente en une succession d’appels distants. La taille réduite n’est pas un objectif suffisant ; la cohésion doit rester compréhensible.

## 9. Cartographier les relations entre modèles

Une context map décrit les contextes et leurs relations. Le DDD Reference présente notamment des relations de partenariat, de client-fournisseur, de conformité, de noyau partagé et de protection par traduction. [1]

Le dessin doit rendre visibles les dépendances et la capacité d’influence. Si notre organisation ne peut pas modifier le modèle du partenaire, elle ne doit pas concevoir l’échange comme si les deux équipes pouvaient en faire évoluer ensemble tous les concepts.

Une couche anticorruption traduit et protège le modèle local. Elle ne se limite pas à renommer des champs. Un code reçu peut signifier réception technique chez le partenaire, sans justifier une confirmation d’exécution dans notre modèle. [1][4]

```text
Modèle du partenaire
        ↓
Traduction des données et de leur sens
        ↓
Faits exploitables par le modèle Paiements
```

Le mécanisme de transport ne supprime pas cette traduction. Un événement transmis par un broker reste exprimé dans un langage et un modèle donnés. Les travaux de Domain Language sur l’intégration des systèmes existants soulignent précisément cette nécessité. [4]

## 10. Passer des données aux comportements

Une entité conserve une identité à travers les changements. Un objet-valeur est défini par ses valeurs et les opérations associées, sans identité propre. Le DDD Reference distingue ces éléments et recommande de traiter les objets-valeur comme immuables. [1]

Dans notre exemple, deux paiements peuvent partager le même montant sans être le même paiement. Un montant associe une valeur et une devise ; les règles d’égalité, d’arrondi et d’opérations doivent être définies selon le contexte.

Le domaine doit aussi exprimer les actions significatives. Une méthode `confirmerExecution` indique une intention plus précise que `setStatut`. Elle peut vérifier les conditions nécessaires au changement.

Le nom de la méthode ne suffit cependant pas à produire un effet externe. Confirmer dans le modèle que l’exécution a été établie n’est pas exécuter un transfert chez le partenaire. La conception doit préserver cette différence.

## 11. Les agrégats protègent une frontière de cohérence

Un agrégat regroupe des objets autour d’une racine qui contrôle les changements nécessaires à la cohérence de l’ensemble. Sa responsabilité inclut la protection des invariants pertinents. [1][3]

Dans le cas fictif, un agrégat Paiement pourrait empêcher une transition contradictoire ou la modification d’informations devenues fixes après une étape définie. Ces règles doivent être explicitées avec le métier.

| Règle proposée | Question de conception |
|---|---|
| Pas de confirmation sans fait reconnu | Quelle preuve est acceptée par le contrat ? |
| Pas de transition contradictoire silencieuse | Comment traiter un résultat tardif incompatible ? |
| Préserver l’identité du paiement | Quelle référence identifie la même intention ? |

L’agrégat n’est pas tout le système bancaire chargé dans un objet. Sa taille doit être guidée par les règles à garder cohérentes, les accès concurrents et les usages.

Une vérification dans une méthode en mémoire ne suffit pas à arbitrer deux opérations concurrentes. L’implémentation doit relier la frontière métier à une stratégie transactionnelle et de concurrence. Les mécanismes seront étudiés dans les tomes suivants.

## 12. Séparer domaine, orchestration et infrastructure

Le domaine porte les concepts et règles. La couche applicative orchestre les cas d’usage. L’infrastructure réalise les échanges et la persistance. La documentation Microsoft explique cette séparation tout en rappelant qu’ignorer les détails de persistance ne permet pas d’ignorer ses contraintes. [2]

```text
Cas d’usage : enregistrer une confirmation
    1. Identifier et charger le paiement
    2. Faire appliquer la règle par le modèle
    3. Persister selon la stratégie de concurrence
    4. Préparer les suites nécessaires au cas d’usage
```

Le contrôleur adapte l’entrée. Le repository permet d’accéder à l’agrégat selon le contrat retenu. L’adaptateur du partenaire traduit les informations externes. Aucune de ces responsabilités ne doit dupliquer silencieusement les invariants dans tous les chemins d’accès.

Un service de domaine porte une opération métier qui ne trouve pas naturellement sa place dans une entité ou un objet-valeur. Il ne doit pas devenir un service technique géant contenant tout le système. [1]

Cette séparation peut être réalisée avec des architectures en couches ou à ports et adaptateurs. Le DDD Reference cite explicitement l’architecture hexagonale comme une approche compatible avec l’isolation recherchée. [1]

## 13. Validation de forme et validité métier

Un montant illisible et une transition interdite ne sont pas le même problème. L’interface peut contrôler les champs requis et leur format. Le domaine doit préserver les règles quel que soit le chemin qui l’appelle.

La documentation sur les invariants distingue les validations de présentation des validations du domaine. Elle insiste également sur le risque de laisser un objet partiellement modifié après l’échec d’une opération. [3]

Dans notre exemple, la réception d’un résultat incompatible ne doit pas modifier quelques propriétés puis échouer en laissant un état incohérent. La stratégie d’erreur doit être pensée avec les changements de l’agrégat et la transaction.

Il faut aussi éviter d’exiger d’une demande encore en cours de saisie qu’elle soit déjà une entité métier valide. Le modèle d’entrée, le modèle de consultation et le modèle de domaine ont des responsabilités différentes. [2][3]

## 14. Faire apparaître l’incertitude sans confondre observation et réalité

Notre application peut ignorer le résultat alors que le partenaire a déjà exécuté l’opération. Le statut connu localement n’est donc pas toujours l’état complet de la réalité distante.

Cette distinction doit guider le modèle. Selon les besoins, il peut être utile de séparer l’état de prise en charge, la connaissance du résultat et la situation de résolution. Un unique champ peut être suffisant dans un cas simple ; il peut devenir ambigu lorsqu’il mélange plusieurs dimensions indépendantes.

```text
Demande locale : prise en charge
Résultat d’exécution connu : indéterminé
Résolution : recherche en cours
```

Ces trois lignes décrivent une option de modélisation pour notre cas fictif. Elles ne constituent pas un modèle standard de paiement.

Cette option doit être vérifiée avec des scénarios : réponse perdue, confirmation tardive, résultat contradictoire et demande répétée. Un modèle utile permet d’expliquer ce que le système sait, ce qu’il ne sait pas et ce qui peut légitimement changer.

## 15. Événements de domaine et intégration

Un événement de domaine représente un fait significatif pour le métier. Il ne se confond pas avec tous les événements techniques émis par le logiciel. Le DDD Reference en fait un élément explicite du modèle. [1]

`ExecutionConfirmee` exprime un fait différent de `RequeteHttpTerminee`. Le second peut contribuer à l’observation technique sans établir le premier.

Lorsque ce fait est communiqué à un autre contexte, un contrat d’intégration doit être défini. Les données et le vocabulaire exposés ne sont pas automatiquement la représentation interne de l’agrégat. La traduction entre modèles reste nécessaire. [4]

Utiliser de tels événements n’impose pas que tous les états soient reconstruits depuis un journal d’événements. Dans son texte de référence, Eric Evans présente CQRS et Event Sourcing comme des options architecturales associées aux évolutions de DDD, pas comme son unique réalisation. [1]

## 16. Faire évoluer le modèle par les exemples

Le modèle initial doit pouvoir être corrigé. Le DDD Reference décrit un approfondissement progressif par refactoring, en lien étroit avec les experts métier. [1]

Dans notre cas, une première classe peut mélanger paiement et tentative d’envoi. Les scénarios révèlent ensuite qu’un même paiement peut donner lieu à plusieurs échanges techniques. Séparer les concepts peut alors rendre les reprises plus compréhensibles.

Le changement doit atteindre le vocabulaire, les responsabilités et les tests. Renommer une classe sans corriger les contrats ni les usages laisse subsister l’ancien modèle sous un nouveau nom.

Les exemples servent de repères. Un scénario comme « réponse perdue après réception distante » doit rester explicable après la modification. Si le nouveau modèle le rend impossible à représenter, l’amélioration technique a peut-être supprimé une distinction essentielle.

## 17. Introduire DDD dans l’existant

Un système historique peut contenir une compréhension métier précieuse, même si elle est dispersée dans des écrans, traitements et tables. Il ne faut ni la recopier sans examen ni supposer qu’une réécriture la retrouvera spontanément.

Une stratégie consiste à choisir un périmètre limité, à construire un modèle adapté et à protéger ses échanges par traduction. Les travaux de Domain Language sur les systèmes existants décrivent notamment l’introduction d’un contexte limité entouré d’une couche anticorruption. [4]

Dans notre application fictive, le rapprochement des résultats pourrait être étudié comme un tel périmètre. L’équipe doit d’abord connaître les références disponibles et les significations réellement portées par l’existant.

Une nouvelle frontière ne supprime pas les dépendances de données. Si le nouveau modèle utilise une copie, il faut examiner sa fraîcheur, sa reconstruction et ses responsabilités d’écriture. L’isolation conceptuelle n’est pas une garantie automatique d’autonomie opérationnelle.

## 18. Quand une approche plus simple suffit

DDD apporte surtout de la valeur lorsque la compréhension du métier et ses règles constituent une difficulté importante. La documentation Microsoft recommande des réalisations plus simples pour les responsabilités essentiellement CRUD. [2]

Il ne faut donc pas imposer un agrégat riche à chaque liste de paramètres ni mesurer la réussite au nombre d’interfaces. Un vocabulaire clair et des responsabilités explicites restent utiles, mais tous les patterns tactiques ne sont pas obligatoires partout.

Les signes d’une mauvaise application sont reconnaissables : classes métier sans comportements, règles dupliquées dans les contrôleurs, modèle universel partagé sans contexte, entités transformées mécaniquement en services et objets techniques baptisés agrégats.

Le critère décisif reste la capacité du modèle à expliquer et protéger les comportements nécessaires. Si la structure ajoutée rend un besoin simple plus difficile sans améliorer cette capacité, il faut revoir le choix.

## 19. Exercice : corriger un modèle trop pauvre

Une application utilise une classe `Paiement` contenant un identifiant et un entier `statut`. Un traitement met le statut à « échec » après un timeout. Une API permet de remplacer librement ce statut. L’écran affiche « terminé » pour toute réponse HTTP réussie.

Identifier les ambiguïtés, proposer un vocabulaire et répartir les responsabilités entre interface, application, domaine et adaptation du partenaire.

Une réponse argumentée distingue réception technique, résultat établi et absence de connaissance. Elle supprime la mutation libre lorsqu’elle contourne les invariants. Elle précise quelles informations du partenaire peuvent autoriser une confirmation et lesquelles exigent une recherche.

Le contrôleur adapte la demande et les erreurs ; l’application coordonne le cas d’usage ; le domaine protège les transitions ; l’adaptateur traduit le contrat externe. La concurrence et la répétition demandent ensuite des mécanismes de persistance et d’intégration à vérifier.

L’exercice ne permet pas de déduire le nombre de microservices. Le problème initial concerne le modèle et ses règles, pas nécessairement le déploiement.

## 20. Concevoir avec le métier, vérifier dans le logiciel

DDD met le modèle au travail. Le langage partagé aide les acteurs à comprendre le même problème dans un contexte explicite. Les frontières reconnaissent la pluralité des modèles. Les éléments tactiques traduisent les règles dans des comportements maîtrisables.

La démarche ne dispense ni des exigences de qualité ni de la conception technique. Elle leur donne des concepts et des responsabilités auxquels se rattacher. Un modèle de paiement doit expliquer l’incertitude ; sa réalisation doit aussi démontrer la conservation des informations, le contrôle concurrent et la reprise.

Le deuxième tome développera ces mécanismes. Le chapitre suivant abordera SABSA pour examiner comment relier les objectifs métier et les risques à l’architecture de sécurité.

## Références

[1] Eric Evans, *Domain-Driven Design Reference — Definitions and Pattern Summaries*, Domain Language, 2015 ; définitions, langage partagé, contextes, éléments tactiques et conception stratégique : https://www.domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf

[2] Microsoft Learn, *Designing a DDD-oriented microservice*, complexité métier, frontières, couches et limites liées à la persistance : https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice

[3] Microsoft Learn, *Designing validations in the domain model layer*, invariants et distinction des validations : https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/domain-model-layer-validations

[4] Domain Language, *Getting Started with DDD When Surrounded by Legacy Systems*, contextes limités et traduction : https://www.domainlanguage.com/wp-content/uploads/2016/04/GettingStartedWithDDDWhenSurroundedByLegacySystemsV1.pdf

[Chapitre précédent](chapitre-07.md) · [Sommaire](../SOMMAIRE.md)
