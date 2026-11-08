# Chapitre 8 — CQRS et les modèles de consultation

## 1. Adapter deux responsabilités différentes

Command Query Responsibility Segregation distingue les modèles utilisés pour décider et modifier de ceux utilisés pour consulter. Microsoft présente aussi une réalisation partageant le même stockage ; CQRS n’exige donc pas systématiquement deux bases, des microservices ou de l’Event Sourcing. [1]

Dans notre étude, l’écriture protège les transitions d’un paiement. L’assistance recherche des dossiers, des attentes anciennes et leurs références. Une vue adaptée à cette recherche n’a pas besoin d’exposer les objets internes du domaine.

## 2. Commencer par le coût réel

Une lecture SQL ou une projection synchrone peut suffire. Une projection asynchrone devient intéressante si la forme, le volume ou l’autonomie de consultation justifient ses coûts.

| Choix | Conséquence à examiner |
|---|---|
| Modèles distincts, stockage commun | Séparation logique sans synchronisation entre bases |
| Vue actualisée dans la transaction locale | Coût d’écriture et couplage transactionnel |
| Projection asynchrone | Retard, répétition et reconstruction |

Une vue matérialisée n’est pas automatiquement une nouvelle autorité métier. Une action sensible doit encore être validée par le propriétaire des règles.

## 3. Traiter la fraîcheur comme un contrat

Après une commande réussie, la projection peut afficher l’ancien état. Microsoft identifie cette difficulté lorsque les stockages sont séparés. [1]

Le parcours peut afficher la prise en charge renvoyée par la commande, attendre une position de projection ou annoncer explicitement la connaissance disponible. Attendre sans limite la projection réintroduit une dépendance qu’il faut assumer.

La date d’actualisation d’une ligne ne prouve pas que tous les messages précédents ont été traités. Un suivi de position ou de version doit avoir un sens défini.

## 4. Prévenir les régressions

Une projection de changements doit reconnaître les doublons et gérer les trous. Ignorer simplement toute version inférieure à la plus grande observée est incorrect si une étape manquante portait un changement indispensable.

Pour une projection de snapshots complets, remplacer par une version plus récente peut être valide si le contrat couvre toutes les informations nécessaires. La stratégie vient du contenu des événements, pas du seul nom du pattern.

L’Inbox et l’actualisation doivent partager la transaction locale lorsqu’on utilise ce mécanisme.

## 5. Reconstruire sans mélanger les générations

Nous proposons une nouvelle génération de vue, construite à partir d’un historique complet ou d’un snapshot avec point de reprise cohérent. Elle rattrape les nouveaux faits, subit des contrôles, puis devient la source de lecture par une bascule maîtrisée.

Un historique expiré ne permet pas une reconstruction complète. La procédure doit connaître sa source, sa couverture, sa version de transformation et son comportement lors d’un échec.

Les projections dupliquent parfois des informations personnelles : droits de lecture, suppression et rétention doivent rester cohérents avec leur usage.

## 6. Exercice : le paiement disparaît après création

L’écriture réussit mais l’écran recherche immédiatement dans une vue en retard et annonce une absence définitive.

La correction distingue absence dans la projection et refus de création, utilise la référence reçue et propose un parcours d’attente borné. Elle vérifie aussi le comportement après rattrapage et reconstruction.

## Référence

[1] Microsoft Learn, *CQRS pattern* : https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs

[Précédent](chapitre-07.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-09.md)
