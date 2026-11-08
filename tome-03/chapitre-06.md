# Chapitre 6 — Fiabiliser la publication : double écriture et Outbox

## 1. Examiner les deux ordres possibles

Après une confirmation, Paiements doit enregistrer le fait et le publier. Écrire la base puis envoyer laisse une fenêtre où le fait existe sans publication. Publier puis écrire peut diffuser un fait que le rollback annulera localement.

L’Outbox transactionnelle enregistre les données et la publication à transmettre dans une même transaction locale. Un relais transmet ensuite les publications validées. AWS et Chris Richardson présentent cette réponse au problème de double écriture. [1][2]

## 2. Conserver un travail interprétable

```text
Transaction locale
  modifier l’état selon les règles
  ajouter une publication avec identité stable
Commit

Relais
  lire les publications validées éligibles
  transmettre et vérifier la réponse prévue
  enregistrer leur avancement
```

La publication conserve type, version, référence métier et contenu nécessaire. Le relais ne doit pas reconstruire un ancien fait en lisant seulement l’état courant, qui a pu évoluer.

Si l’écriture métier et l’Outbox utilisent deux stockages sans atomicité commune, nommer la seconde table Outbox ne résout pas le problème.

## 3. Comprendre la fenêtre restante

Le relais peut transmettre puis s’arrêter avant de marquer le travail terminé. Il le transmettra alors de nouveau. L’identité de publication doit rester stable pour permettre la reconnaissance par le consommateur. [1]

Marquer avant d’envoyer inverse le risque : un arrêt peut rendre la publication définitivement invisible au relais. Le compromis doit privilégier une répétition maîtrisée plutôt qu’un oubli silencieux.

La progression dépend encore de la reprise du relais, de la conservation et de la disponibilité des dépendances. L’Outbox ne garantit pas un délai universel ni le succès métier de tous les consommateurs.

## 4. Coordonner les relais et l’ordre

Plusieurs relais doivent se répartir le travail par une réservation ou un contrôle atomique adapté. Une réservation expirée permet une reprise, mais l’ancien relais peut encore avoir envoyé : les doublons restent possibles.

L’ordre d’une demande peut imposer de ne pas transmettre son évolution suivante avant la précédente. Trier par une date globale ne suffit pas à résoudre concurrence, publication parallèle et confirmations désordonnées.

Une stratégie de lecture du journal transactionnel peut remplacer le polling dans certaines réalisations. Elle demande des positions durables, un suivi du retard et une politique compatible avec la rétention du journal. [2]

## 5. Distinguer confirmation et routage

RabbitMQ peut confirmer une publication non routable ; le producteur doit traiter les retours selon les options utilisées. La confirmation n’est pas une preuve de consommation métier. [3]

Le relais doit donc vérifier le contrat de transmission réel, traiter les échecs partiels de lots et surveiller ancienneté, volume et erreurs. Une purge ne doit pas supprimer des travaux encore nécessaires à la reprise.

## 6. Exercice : la publication disparaît après un crash

Le code sauvegarde un paiement, appelle le broker puis efface une ligne de suivi. Identifier les transactions et arrêter le processus entre chaque étape.

La correction rend atomiques état et publication, conserve l’identité lors des retries et prouve le traitement des doublons aval. Le chapitre suivant construit cette moitié consommatrice.

## Références

[1] AWS, *Transactional outbox pattern* : https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html

[2] Chris Richardson, *Transactional outbox* : https://microservices.io/patterns/data/transactional-outbox

[3] RabbitMQ, *Consumer Acknowledgements and Publisher Confirms* : https://www.rabbitmq.com/docs/confirms

[Précédent](chapitre-05.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-07.md)
