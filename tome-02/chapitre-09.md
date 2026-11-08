# Chapitre 9 — Organiser les données et les transactions

## 1. Relier la persistance aux règles

Un schéma de données ne constitue pas, seul, le modèle métier. Il doit néanmoins réaliser des propriétés dont dépend la correction du service : identité, unicité, états et conservation durable.

Dans notre système fictif, la demande, la tentative d’échange et la publication à transmettre représentent des concepts différents. Les fusionner dans une ligne mutable peut effacer les informations nécessaires à la reprise.

L’agrégat du chapitre 3 protège une cohérence en mémoire. Ce chapitre examine comment la relier aux garanties du stockage.

## 2. Définir le propriétaire et les représentations

Le propriétaire d’une donnée décide de sa modification selon le contrat. Une projection de consultation peut conserver une copie sans devenir cette autorité.

| Représentation | Responsabilité dans notre cas |
|---|---|
| Demande courante | Porter les règles et la version de l’état |
| Tentatives d’échange | Expliquer les interactions techniques |
| Vue de consultation | Répondre aux lectures et exposer sa fraîcheur |
| Historique utile | Conserver les faits nécessaires à l’explication |

Le mapping peut différer du domaine. Une entité persistée, un agrégat et un objet d’échange n’ont pas automatiquement la même frontière. La reconstruction doit préserver le sens et ne pas rejouer des effets déjà produits.

## 3. Atomicité et isolation ne sont pas interchangeables

Une transaction peut rendre plusieurs écritures atomiques sans empêcher tous les raisonnements incorrects entre transactions concurrentes.

PostgreSQL décrit notamment Read Committed comme une lecture de données validées au début de chaque instruction ; deux SELECT successifs peuvent donc observer des états différents. Ses niveaux plus forts apportent d’autres garanties et peuvent imposer de recommencer une transaction. Ces comportements sont ceux du moteur documenté et ne doivent pas être extrapolés sans vérification à tous les produits. [1]

Il faut partir du scénario. Deux confirmations concurrentes, une lecture suivie d’une correction et une règle portant sur plusieurs lignes ne nécessitent pas automatiquement le même mécanisme.

Le choix de l’isolation doit être testé avec plusieurs transactions réellement indépendantes. Un test qui utilise une seule connexion ne reproduit pas nécessairement la concurrence visée.

## 4. Combiner contraintes et contrôle de version

Une contrainte d’unicité peut protéger une clé métier. Un contrôle de version peut empêcher l’écriture d’un état calculé depuis une version ancienne. Les deux traitent des propriétés différentes.

```text
Demande : identité et version
Reconnaissance d’intention : unicité sur (périmètre, clé)
Publication : identité stable et état de traitement
```

Cette esquisse est conceptuelle. La définition du périmètre et du contenu associé à la clé doit venir du contrat. Une même clé avec un contenu différent ne doit pas être traitée arbitrairement comme une répétition valide.

Le contrôle « chercher puis insérer » n’établit pas, à lui seul, l’unicité sous concurrence. Les contraintes et le traitement de leurs conflits doivent faire partie de la réalisation.

Une version d’agrégat ne protège pas automatiquement une règle couvrant plusieurs agrégats. Il faut examiner une transaction plus large, un autre mécanisme de contrôle ou un processus compatible avec le besoin réel.

## 5. Ne pas étendre implicitement la transaction au réseau

Une transaction locale n’inclut pas automatiquement un appel HTTP ou une publication sur un autre système. Maintenir une transaction ouverte pendant un appel lent peut également prolonger l’utilisation des ressources.

Dans notre conception, la confirmation et l’enregistrement d’une publication à transmettre peuvent être validés dans une transaction locale. Un relais traite ensuite les publications. Cette proposition devra préciser concurrence, ordre et reprise.

Le relais peut publier puis s’arrêter avant d’enregistrer son succès. La répétition reste donc possible. Une table de publication ne remplace pas l’idempotence du consommateur.

Les événements ne changent pas cette contrainte parce qu’ils sont appelés faits métier. Le tome III développera les mécanismes de publication et de cohérence distribuée.

## 6. Faire évoluer le schéma sans casser la coexistence

Une migration doit tenir compte des versions applicatives qui fonctionnent pendant le déploiement.

Nous proposons de séparer extension, migration des données et retrait. Ajouter une nouvelle représentation compatible permet d’introduire un nouveau lecteur ou écrivain. La conversion doit ensuite être vérifiée avant la suppression de l’ancien champ ou format.

| Étape | Question de validation |
|---|---|
| Étendre | Les anciennes versions peuvent-elles encore fonctionner ? |
| Migrer | Les données converties préservent-elles le sens et les contraintes ? |
| Retirer | Existe-t-il encore un consommateur de l’ancienne représentation ? |

Ce séquencement est une méthode proposée, pas une garantie automatique. Un retour à un ancien binaire ne suffit pas toujours à interpréter les données écrites par une nouvelle version.

## 7. Concevoir l’historique et la récupération

Un journal d’audit, une sauvegarde et un event store ne servent pas le même objectif. Conserver des événements comme source de vérité correspond à un choix d’Event Sourcing ; conserver des traces autour d’un état courant n’y équivaut pas. [2]

La restauration doit aussi examiner le partenaire. Il peut avoir réalisé une action après le point de récupération local. La recherche et le rapprochement doivent traiter cette possibilité avant de renvoyer des demandes.

Les données à conserver, supprimer ou anonymiser dépendent du contexte et des obligations applicables. Le livre n’impose aucune durée universelle.

## 8. Exercice : une base restaurée, un résultat oublié

Après restauration, une demande semble non exécutée. Le partenaire l’avait pourtant traitée. Proposer les éléments de données et les contrôles nécessaires avant toute reprise.

La correction conserve une référence stable, distingue connaissance locale et réalité externe, recherche les faits disponibles et interdit une resoumission aveugle. Elle examine le périmètre de perte annoncé et les responsabilités d’investigation.

La qualité de la persistance se mesure à la capacité de réaliser les règles et de récupérer une situation compréhensible, pas seulement à la réussite d’un INSERT.

## Références

[1] PostgreSQL, *Transaction Isolation*, documentation courante consultée, garanties propres au moteur : https://www.postgresql.org/docs/current/transaction-iso.html

[2] Microsoft Learn, *Event Sourcing pattern* : https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing

[Précédent](chapitre-08.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-10.md)
