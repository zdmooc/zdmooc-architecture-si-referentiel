# Chapitre 7 — Fiabiliser la consommation : acquittements, Inbox et reprise

## 1. Choisir le moment où l’on renonce à une livraison

Un acquittement informe le broker que la livraison peut être considérée comme traitée selon le protocole. Il ne valide pas automatiquement une transaction applicative.

RabbitMQ distingue acquittement automatique et manuel, réachemine les livraisons non acquittées après certaines fermetures et avertit contre les boucles de remise immédiate. Ces comportements doivent être vérifiés pour le produit et la configuration retenus. [1]

## 2. Relier Inbox et effet local

L’Inbox durable reconnaît une publication déjà appliquée. Son marqueur et les changements métier locaux doivent être validés ensemble. Microsoft décrit cette variante transactionnelle du consommateur idempotent. [2]

```text
Recevoir et contrôler le message
Transaction locale
  arbitrer l’unicité (consommateur logique, publication)
  si déjà appliqué : ne pas répéter l’effet
  sinon : appliquer les règles et conserver le marqueur
  conserver les publications sortantes éventuelles
Commit
Acquitter la livraison
```

Le périmètre du marqueur distingue les consommateurs indépendants. Consultation et Notifications peuvent toutes deux devoir réagir à une même publication.

Une insertion en conflit doit être traitée selon les règles du moteur ; elle ne doit pas laisser une transaction invalide poursuivie comme si tout avait réussi.

## 3. Vérifier les arrêts

Avant commit, un arrêt ne doit laisser ni effet local partiel ni marqueur définitif. Après commit mais avant acquittement, une nouvelle livraison retrouve le marqueur et ne répète pas l’effet.

Acquitter avant commit peut perdre le travail si la transaction échoue. Écrire le marqueur dans une transaction distincte peut produire le même oubli.

L’effet externe échappe à cette atomicité. Envoyer un courriel ou appeler un partenaire dans la transaction ne rend pas son effet annulable. Il faut un travail durable distinct et une reconnaissance adaptée au destinataire.

## 4. Classer les erreurs et borner les reprises

Une indisponibilité temporaire justifie parfois une nouvelle tentative différée. Un contrat illisible ou une version non prise en charge demande une autre action. Un refus métier peut être un résultat à conserver et publier, non une panne technique à réessayer indéfiniment.

Une quarantaine conserve le message, sa cause et les éléments nécessaires au diagnostic. Elle doit avoir un propriétaire, un accès protégé et une procédure de réinjection.

Déplacer un message peut casser un ordre métier. Si une projection dépend de tous les changements, traiter les suivants sans règle de trou peut la corrompre.

## 5. Rejouer avec une identité cohérente

Une remise technique conserve l’identité de publication. Une transformation de contrat ou une nouvelle décision doit être distinguée et traçable. Générer une nouvelle identité à chaque retry peut neutraliser l’Inbox.

La suppression des marqueurs doit tenir compte de l’historique encore rejouable. Une reconstruction volontaire de projection peut utiliser un nouveau périmètre de consommation, sans déclencher de nouveau les effets externes.

## 6. Exercice : l’effet existe, le broker attend

Le consommateur a validé sa base mais perd sa connexion avant l’acquittement. Définir le comportement à la livraison suivante.

La correction retrouve l’effet par un marqueur atomique, évite sa duplication et acquitte la nouvelle livraison. Elle vérifie aussi les arrivées simultanées sur deux instances.

## Références

[1] RabbitMQ, *Consumer Acknowledgements and Publisher Confirms* : https://www.rabbitmq.com/docs/confirms

[2] Microsoft Learn, *Idempotent Consumer pattern* : https://learn.microsoft.com/en-us/azure/architecture/patterns/idempotent-consumer

[Précédent](chapitre-06.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-08.md)
