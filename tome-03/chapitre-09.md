# Chapitre 9 — Event Sourcing, projections et reconstruction

## 1. Changer la source de vérité

En Event Sourcing, l’état d’une entité est dérivé de la séquence de ses événements conservés. Il ne s’agit pas simplement de publier des événements après avoir modifié une ligne courante. Microsoft distingue ce stockage historique de l’état courant matérialisé. [1]

Un journal technique ou une piste d’audit autour d’un modèle classique ne suffit pas à constituer ce choix. Le modèle de référence, la correction et la reprise doivent effectivement s’appuyer sur le flux.

## 2. Décider puis appliquer

Dans notre variante fictive, une commande charge les événements d’une demande, reconstruit son état, évalue les règles et propose de nouveaux événements.

```text
Historique → reconstruction pure → état courant
Commande + état → décision → événements proposés
Ajout atomique sous version attendue → nouvel historique
```

L’ajout doit vérifier une version attendue afin que deux décisions concurrentes ne soient pas toutes deux acceptées depuis le même passé. En cas de conflit, la commande relit puis réévalue les règles ; elle ne réutilise pas aveuglément sa première décision.

Les événements d’une même décision doivent posséder l’atomicité nécessaire à leur interprétation. Une version métier n’est pas une simple date d’horloge.

## 3. Séparer reconstruction et effets

Appliquer un événement pour reconstruire doit être déterministe dans les conditions définies. Cette fonction ne doit ni contacter le partenaire ni envoyer une notification.

Sinon, relire l’historique répète les effets. Les décisions utilisent des dépendances explicites ; les faits conservés doivent contenir les informations nécessaires pour comprendre ce qui a été décidé.

La publication externe doit aussi être reprise. Si l’event store sert de source de publication, les positions et la rétention du lecteur sont des responsabilités de fiabilité, pas une conséquence magique du stockage événementiel.

## 4. Employer les snapshots comme accélérateurs

Un snapshot évite de rejouer tout l’historique à chaque chargement. Il conserve une position et un format compatibles avec la reconstruction.

Il ne remplace pas automatiquement l’historique de référence. Un snapshot invalide doit pouvoir être écarté si la politique prévoit cette reconstruction. Un résultat différent selon l’existence du snapshot révèle un problème à traiter.

## 5. Faire évoluer et corriger

Un ancien événement garde le sens qu’il avait lors de sa production. Des lecteurs compatibles ou des transformations à la lecture peuvent accompagner l’évolution du contrat. Réinterpréter silencieusement tous les anciens faits selon une règle actuelle peut falsifier l’histoire. [1]

Une correction métier peut ajouter un nouveau fait explicite plutôt qu’effacer le précédent. Cela ne supprime pas les obligations de protection des données. La minimisation, la séparation des informations identifiantes et les politiques applicables doivent être conçues avant de promettre une conservation immuable illimitée.

## 6. Exercice : une reconstruction renvoie les paiements

Le gestionnaire ExecutionConfirmee appelle le partenaire à chaque application, y compris au chargement de l’agrégat.

La correction sépare réduction d’état et effets, contrôle l’ajout concurrent et prouve une reconstruction sans appel externe. Elle compare état obtenu avec et sans snapshot. Event Sourcing reste un choix motivé, non une condition de CQRS ou de l’architecture événementielle.

## Référence

[1] Microsoft Learn, *Event Sourcing pattern* : https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing

[Précédent](chapitre-08.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-10.md)
