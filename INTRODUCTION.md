# Introduction

## Du besoin au système

Une demande de transformation peut commencer par une phrase très courte : accélérer les paiements, simplifier le parcours client, réduire les incidents ou remplacer une plateforme devenue difficile à maintenir. Cette formulation exprime une direction. Elle ne décrit pas encore le système à construire.

Entre l’intention et sa réalisation, plusieurs traductions sont nécessaires. Il faut comprendre les résultats recherchés, identifier les activités concernées, préciser les règles métier, répartir les responsabilités et choisir des mécanismes techniques. Il faut ensuite vérifier que ces mécanismes restent corrects sous charge, lors d’une panne et pendant les évolutions futures.

Ce livre explore ces traductions. Son fil conducteur est la continuité du raisonnement : une décision technique doit pouvoir être reliée à une contrainte ou à un objectif, et une garantie annoncée doit pouvoir être reliée à une preuve.

## Des approches qui ne répondent pas aux mêmes questions

L’architecture d’entreprise donne un contexte à l’évolution du système d’information. Le standard TOGAF la situe notamment dans l’alignement entre transformation et stratégie de l’organisation. Ses domaines métier, données, applications et technologie permettent d’éviter de réduire le sujet à l’infrastructure. [1]

Le Domain-Driven Design, ou DDD, porte une autre attention : comprendre le domaine et construire des modèles adaptés aux cas d’usage. Il ne remplace pas la gouvernance d’une transformation. Il aide à concevoir le logiciel et à délimiter les modèles dans lesquels les règles métier prennent leur sens. [2]

Les styles architecturaux répondent encore à une autre question : comment organiser le système ? Une architecture en couches, des microservices ou des composants événementiels présentent des caractéristiques et des compromis différents. Ils ne constituent pas des niveaux de maturité que toutes les organisations devraient franchir dans le même ordre. [3]

L’architecture hexagonale s’intéresse notamment à l’organisation des dépendances autour du cœur applicatif. Une application déployée comme une seule unité peut donc être structurée avec des principes de séparation internes exigeants. Le découpage logique et le déploiement physique ne doivent pas être confondus. [4]

## Quatre tomes, un même raisonnement

Le premier tome établit les fondations. Il distingue les niveaux d’architecture et explique comment les démarches, les modèles et la gouvernance contribuent à la transformation. Il présente les cadres sans confondre méthode, langage de représentation et référentiel sectoriel.

Le deuxième tome suit la conception des solutions et des applications. Il traite les frontières métier, les modèles DDD, les couches, les ports et adaptateurs, les services et les contrats. La question principale devient : comment répartir les responsabilités sans perdre la cohérence du besoin ?

Le troisième tome examine les échanges et les données dans les systèmes distribués. La panne partielle, le doublon et le résultat inconnu y occupent une place centrale. Les patterns ne sont pas étudiés seulement dans leur scénario nominal, mais aussi à travers leurs limites.

Le quatrième tome relie la conception à la production. Il traite les plateformes, la sécurité technique, l’observabilité, la capacité et la modernisation. Une migration n’y sera pas considérée comme réussie parce qu’une application démarre sur une nouvelle plateforme, mais parce que les comportements attendus et les conditions d’exploitation ont été vérifiés.

## Lire avec un cas concret en tête

Une organisation fictive souhaite améliorer le suivi des paiements. Les clients doivent comprendre si une demande est reçue, en cours, rejetée ou exécutée. Le service d’assistance doit retrouver les opérations incertaines. Les équipes techniques doivent pouvoir faire évoluer l’application et la reprendre après incident.

Ce cas servira à poser des questions de plus en plus précises. Que signifie un statut ? Qui est autorisé à le modifier ? Comment éviter une seconde exécution ? Que faire si le partenaire reçoit une demande mais que sa réponse est perdue ? Ces questions rendent visibles les liens entre modèle métier et mécanismes techniques.

Le lecteur peut suivre les tomes dans l’ordre ou consulter un chapitre pour éclairer une décision. Dans les deux cas, il gagnera à conserver trois questions : quel problème est traité, quel compromis est accepté et comment le résultat sera-t-il vérifié ?

## Références

[1] The Open Group, *The TOGAF Standard — Introduction*, domaines d’architecture et ADM : https://pubs.opengroup.org/togaf-standard/introduction/chap04.html

[2] Microsoft Learn, *Designing a DDD-oriented microservice* : https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice

[3] Microsoft Learn, *Architecture styles* : https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/

[4] Microsoft Learn, *Common web application architectures* : https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures

[Sommaire](SOMMAIRE.md) · [Commencer le chapitre 1](tome-01/chapitre-01.md)
