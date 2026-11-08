# Chapitre 10 — Saga, orchestration et compensation

## 1. Coordonner des transactions locales

Une saga organise un processus composé de transactions locales, avec des suites et des compensations définies. Elle ne rend pas toutes les étapes atomiques ni invisibles aux autres acteurs pendant leur déroulement. Microsoft distingue les approches par chorégraphie et orchestration. [1]

Pour notre étude, un processus peut réserver une ressource, demander une exécution puis libérer ce qui n’est plus nécessaire. Ces étapes restent hypothétiques : les possibilités de réservation et de compensation doivent être établies avec les participants réels.

## 2. Ne pas appeler rollback une nouvelle opération métier

Une compensation tente de rétablir une situation acceptable selon les règles. Elle peut laisser des traces, coûter des frais ou nécessiter une approbation. Elle peut aussi échouer.

Une exécution irréversible ne se défait pas par l’effacement d’une ligne locale. Un remboursement, s’il est autorisé, est une nouvelle opération avec son propre cycle de vie, pas une annulation du passé.

Le processus doit identifier les étapes compensables, les jalons qui changent les possibilités et les opérations à reprendre jusqu’à une issue acceptable. [1]

## 3. Comparer les coordinations

| Approche | Organisation | Vigilance |
|---|---|---|
| Chorégraphie | Réactions des participants aux événements | Visibilité du parcours et boucles |
| Orchestration | Coordinateur qui conserve les étapes et émet des commandes | Disponibilité et complexité du coordinateur |

Un orchestrateur peut être répliqué si son état et l’arbitrage sont conçus. Il ne doit pas absorber les règles internes de tous les participants. La chorégraphie ne supprime pas la responsabilité du processus de bout en bout.

## 4. Conserver les transitions et les intentions

Nous proposons pour l’orchestration un état durable contenant référence du processus, étape, échéance, résultats reconnus et intentions sortantes.

La réception d’un résultat modifie cet état et conserve la commande suivante dans une même transaction locale avec Inbox et Outbox lorsque ces mécanismes sont retenus.

Une réponse répétée ne doit pas ouvrir deux compensations. Une réponse tardive doit être interprétée selon l’état actuel, sans être ignorée si elle révèle un fait important. Les timers doivent être persistants ou reconstructibles ; un timer en mémoire ne doit pas être le seul moyen de détecter une échéance.

## 5. Traiter les interférences

Entre deux étapes, une autre opération peut agir sur la même ressource. Une réservation, une version ou une règle d’accès peut limiter certaines interférences, sans créer une isolation globale implicite.

Un timeout de participant ne signifie pas refus. Compenser avant d’avoir clarifié une exécution peut produire une combinaison dangereuse. Les états inconnus et les interventions doivent donc appartenir au processus.

## 6. Exercice : compensation automatique après timeout

Le coordinateur libère la réservation dès que l’appel d’exécution dépasse son délai. Le partenaire confirme ensuite l’exécution.

La correction distingue délai technique et résultat métier, introduit une clarification et définit la transition tardive. Elle examine aussi la panne de compensation, les droits d’intervention et les critères de clôture. Un état terminal doit exprimer les faits acquis, pas seulement l’épuisement des retries.

## Référence

[1] Microsoft Learn, *Saga design pattern* : https://learn.microsoft.com/en-us/azure/architecture/patterns/saga

[Précédent](chapitre-09.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-11.md)
