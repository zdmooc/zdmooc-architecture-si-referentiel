# Chapitre 14 — Qualifier les garanties par les scénarios de panne

## 1. Transformer une promesse en propriété vérifiable

Les patterns précédents répondent à des fenêtres précises. Leur présence dans un diagramme ne prouve pas leur réalisation.

Notre qualification part d’invariants : une intention reconnue ne crée pas plusieurs opérations locales ; une confirmation contradictoire n’écrase pas silencieusement un fait ; un travail reçu durablement reste retrouvable ; une reprise n’accorde pas de nouveaux droits.

Il faut également vérifier la progression : les travaux traitables finissent par avancer dans les conditions annoncées. Ne rien exécuter peut éviter les doublons tout en rendant le système inutilisable.

## 2. Préparer un environnement représentatif

Les tests utilisent des instances et transactions indépendantes, les modes d’acquittement retenus et les contraintes du stockage réel. Un faux repository ne démontre pas l’arbitrage sous concurrence.

Un partenaire simulé doit pouvoir accepter puis perdre sa réponse, retarder la visibilité d’un résultat et renvoyer une réponse tardive. Les attentes doivent être contrôlées plutôt que fondées uniquement sur des pauses arbitraires.

La documentation PostgreSQL et celle de RabbitMQ permettent de définir les comportements à vérifier, sans remplacer les essais de notre configuration. [1][2]

## 3. Couvrir les fenêtres de panne

| Perturbation injectée | Propriété observée |
|---|---|
| Arrêt avant commit métier et Outbox | Aucun fait partiel ni publication indue |
| Arrêt après commit, avant publication | Travail durable retrouvé par le relais |
| Publication reçue, confirmation perdue | Répétition possible avec identité conservée |
| Commit consommateur, acquittement perdu | Livraison répétée sans nouvel effet local |
| Deux instances sur la même intention | Arbitrage durable de l’unicité |
| Partenaire exécutant sans réponse | Résultat inconnu et clarification contrôlée |
| Message manquant ou désordonné | Détection ou stratégie de projection appropriée |
| Compensation indisponible | Cas durable non clôturé artificiellement |
| Restauration après effet distant | Rapprochement avant resoumission |

Pour chaque scénario, la preuve inclut état durable, effet externe observé, travail restant et action de reprise. Le seul code HTTP retourné ne suffit pas.

## 4. Vérifier charge, rétention et rattrapage

La qualification doit prolonger les indisponibilités, atteindre les limites d’admission et vérifier le retour à la normale. Le rattrapage ne doit pas saturer de nouveau le partenaire ; les retries demandent des limites et une dispersion adaptées. [3]

Les purges de clés, d’Inbox, d’Outbox et d’historiques doivent être confrontées aux rejouages et restaurations possibles. Une garantie valable uniquement avant la première purge est insuffisante si le contrat autorise des répétitions plus tardives.

Les projections reconstruites doivent être comparées à une source de référence et basculées sans mélanger les générations.

## 5. Qualifier les interventions

Les fonctions de réinjection, de rapprochement et de compensation doivent appliquer les droits, enregistrer la justification et résister elles-mêmes à la répétition.

Il faut aussi tester les erreurs de manipulation : mauvais périmètre, ancien message ou demande de reprise déjà exécutée. Un outil opérateur n’est pas exempt des règles parce qu’il n’est pas public.

## 6. Délimiter honnêtement les conclusions

Le dossier de preuve conserve versions, configuration, volumes, ordonnancement, critères et résultats effectivement observés. Une expérience réussie ne démontre pas toutes les pannes possibles.

Les scénarios proposés dans ce tome n’ont pas été exécutés ici. Ils constituent un programme de qualification, pas une certification ni un bilan de production.

Exercice final : la solution annonce exactement une fois de bout en bout grâce au broker. Délimiter la garantie du broker, puis examiner transaction locale, effet partenaire, rétention et restauration. La réponse doit identifier ce qui est démontré et ce qui reste conditionnel.

## 7. Conclusion du tome III

Une intégration fiable relie le sens des échanges à des états durables, des frontières de transaction et des procédures de reprise. Outbox et Inbox traitent des fenêtres complémentaires ; l’idempotence ne remplace pas la clarification d’un résultat externe ; une saga ne remplace pas une transaction globale.

CQRS et Event Sourcing répondent à des besoins distincts. BIAN apporte un référentiel de responsabilités sans imposer une topologie. Tous ces choix doivent rester reliés aux engagements du parcours et à leurs preuves.

Le tome IV abordera les plateformes, l’exploitation et la modernisation : déployer et observer ces mécanismes, organiser la récupération et faire évoluer le patrimoine sans perdre leur sens.

## Références

[1] PostgreSQL, *Transaction Isolation* : https://www.postgresql.org/docs/current/transaction-iso.html

[2] RabbitMQ, *Consumer Acknowledgements and Publisher Confirms* : https://www.rabbitmq.com/docs/confirms

[3] AWS Builders’ Library, *Timeouts, retries, and backoff with jitter* : https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/

[Précédent](chapitre-13.md) · [Sommaire](../SOMMAIRE.md)
