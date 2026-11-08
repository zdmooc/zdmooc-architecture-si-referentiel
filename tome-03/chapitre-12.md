# Chapitre 12 — Résultats inconnus et rapprochement

## 1. Une absence de réponse n’est pas un résultat négatif

Le partenaire peut exécuter une demande puis perdre sa réponse. Notre application peut aussi recevoir le résultat et s’arrêter avant sa conservation. AWS rappelle explicitement qu’un timeout ne signifie pas absence d’effet. [1]

Le problème est une différence de connaissance. Il ne doit pas être masqué par un statut ERREUR dont les utilisateurs déduiraient une autorisation de recommencer.

## 2. Conserver les références avant l’interaction

Dans notre conception, la demande possède une identité stable, une référence d’échange compatible avec le partenaire et des tentatives identifiables. L’intention à traiter doit être durable avant l’interaction selon le protocole retenu.

Une trace d’envoi indique ce que nous avons tenté, pas ce que le partenaire a exécuté. Même un jalon local écrit avant l’appel laisse une ambiguïté si le processus s’arrête entre ce jalon et l’envoi réel.

| Observation | Suite proposée |
|---|---|
| Confirmation contractuellement reconnue | Appliquer les règles de confirmation |
| Refus définitif reconnu | Appliquer les règles de refus |
| Timeout ou réponse inexploitable | Rechercher sans inventer un résultat |
| Informations contradictoires | Ouvrir un cas explicite |

## 3. Définir la portée d’une recherche

Une interrogation doit préciser l’identité recherchée, la couverture et le caractère définitif de la réponse. Une absence de correspondance immédiate peut provenir d’un retard d’indexation ou d’un traitement encore en cours.

La décision de resoumettre exige donc une garantie de reconnaissance de la même intention ou une preuve compatible avec le risque. Aucune durée d’attente universelle ne fournit cette preuve.

La recherche elle-même consomme des ressources : fréquence, ancienneté et priorité doivent être bornées.

## 4. Organiser le rapprochement

Nous proposons de comparer les faits locaux avec une source partenaire dont la période et la couverture sont connues. Le rapprochement distingue correspondances, éléments manquants de chaque côté, doublons et divergences de données.

Un total identique ne démontre pas la correspondance de chaque opération. Le rapprochement doit utiliser des références et des critères explicites, puis conserver la provenance de ses conclusions.

Une correspondance incertaine ne doit pas produire automatiquement un nouvel effet. Elle devient un cas attribué, avec justification, permissions et éventuellement approbation supplémentaire selon le risque.

## 5. Traiter réponses tardives et restauration

Une réponse tardive peut résoudre une attente. Si elle contredit un fait reconnu, elle appelle une règle de résolution et une trace ; elle ne doit pas écraser silencieusement la valeur précédente.

Après une restauration, le partenaire peut connaître des effets absents de la base restaurée. Les références, sources de rapprochement et horizons de rétention doivent permettre d’examiner ce décalage avant la reprise des émissions.

La protection des preuves ne justifie pas une copie illimitée des données sensibles. L’accès et la conservation doivent répondre au contexte réel.

## 6. Exercice : pas trouvé, donc renvoyé

Un moteur renvoie une demande sous une nouvelle référence après une recherche négative unique. Le premier traitement termine ensuite.

La correction examine la finalité de la réponse négative, la reconnaissance d’intention et le traitement des deux faits. Elle propose un processus qui peut rester non résolu plutôt que promettre une sûreté non démontrée.

## Référence

[1] AWS Builders’ Library, *Timeouts, retries, and backoff with jitter*, limites de connaissance après timeout : https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/

[Précédent](chapitre-11.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-13.md)
