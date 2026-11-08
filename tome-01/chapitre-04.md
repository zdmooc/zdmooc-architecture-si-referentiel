# Chapitre 4 — Les exigences et les attributs de qualité

## 1. Sortir des adjectifs

« Le système doit être rapide, disponible et sécurisé. » La demande paraît raisonnable, mais elle ne permet pas de choisir une architecture ni de vérifier sa réussite. Rapide pour quelle opération ? Disponible pour quel utilisateur ? Sécurisé contre quelles actions et dans quelles conditions ?

Dans notre système fictif de paiements, la réception d’une demande, l’exécution par le partenaire et la consultation du résultat constituent des opérations différentes. Répondre vite à la réception ne garantit pas une exécution rapide. Afficher une page ne garantit pas que les données soient exactes. Redémarrer une instance ne prouve pas qu’une opération interrompue sera correctement reprise.

Le travail sur les exigences consiste à rendre ces distinctions explicites. L’enjeu n’est pas de transformer chaque attente en formule compliquée. Il est de définir ce que l’on observera pour décider si le service répond au besoin.

## 2. Fonctions, qualités et contraintes

Une exigence fonctionnelle décrit une capacité ou un comportement : soumettre une demande, consulter son état, refuser une transition interdite. Une exigence de qualité précise comment le service doit se comporter dans une situation : délai, continuité, protection, facilité de modification ou récupération.

Une contrainte limite les possibilités de conception. Elle peut imposer un contrat partenaire, un environnement technique ou une obligation issue du contexte. Il faut en connaître l’origine : une préférence n’est pas nécessairement une contrainte, et une contrainte historique peut parfois être renégociée.

La séparation n’est pas absolue. Contrôler une autorisation implique des comportements fonctionnels tout en contribuant à la sécurité. La distinction sert à compléter l’analyse, pas à répartir artificiellement les exigences dans des cases étanches.

| Attente | Formulation à approfondir |
|---|---|
| Consulter un paiement | Qui peut consulter quoi, avec quelles informations ? |
| Consulter rapidement | Quel délai, quelle charge et quel point de mesure ? |
| Consulter une information fiable | Quelle autorité et quelle fraîcheur ? |
| Reprendre après incident | Quels états restaurer, avec quelles preuves ? |

## 3. Ce qui rend une exigence structurante

Toutes les exigences ne transforment pas l’architecture de la même manière. Un changement de libellé peut rester local. Une obligation de reprise sur un autre site, une règle de cohérence forte ou un besoin d’isolation entre utilisateurs peut modifier plusieurs décisions à la fois.

Pour notre analyse, une exigence devient particulièrement structurante lorsqu’elle affecte les frontières, les transactions, les données, les dépendances ou les conditions de déploiement. Il faut l’identifier assez tôt pour ne pas découvrir trop tard qu’une propriété essentielle est incompatible avec les choix réalisés.

L’incertitude compte également. Une exigence apparemment simple, fondée sur une garantie partenaire non vérifiée, peut être plus risquée qu’une exigence complexe mais bien comprise.

La démarche ne consiste donc pas à définir absolument tout avant de commencer. Elle consiste à faire émerger les exigences qui orientent les décisions et à préciser progressivement les autres. Le SEI décrit ce raffinement des scénarios de qualité comme un travail évolutif sur le stimulus, le contexte et la réponse attendue. [1]

## 4. Construire un scénario de qualité

Le Software Engineering Institute propose six éléments pour un scénario d’attribut de qualité : source du stimulus, stimulus, élément concerné, environnement, réponse et mesure de la réponse. [2]

La source indique qui ou quoi déclenche la situation. Le stimulus décrit ce qui arrive. L’élément concerné précise la portée. L’environnement décrit les conditions. La réponse explique le comportement attendu. La mesure rend ce comportement évaluable.

Prenons une consultation dans un exemple chiffré entièrement fictif :

| Élément | Proposition pour l’exercice |
|---|---|
| Source | Utilisateur autorisé |
| Stimulus | Demande de consultation d’un paiement |
| Élément concerné | Parcours de consultation de bout en bout |
| Environnement | Charge de 200 consultations par seconde, jeu de données et durée d’essai définis |
| Réponse | Retourner le dernier état connu et sa date de mise à jour |
| Mesure | Au moins 99 % des consultations admissibles correctement réalisées en moins de 800 ms, avec un taux d’échec suivi séparément |

Ces valeurs illustrent la méthode. Elles ne constituent ni un engagement de service ni une recommandation bancaire. La précision du chiffre ne prouve pas sa pertinence : il faut le relier au besoin et à des observations.

Un scénario incomplet peut rester utile pour découvrir une question. Il ne doit cependant pas être présenté comme un critère d’acceptation achevé.

## 5. Définir le succès avant de mesurer

Compter les réponses HTTP ne suffit pas à mesurer le service métier. Une réponse reçue rapidement peut contenir un état incorrect ou révéler des informations à un utilisateur non autorisé.

Pour chaque mesure, il faut définir la population observée et ce qui constitue un bon résultat. Une demande invalide rejetée conformément au contrat n’est pas nécessairement une défaillance. Inversement, une erreur dissimulée derrière une réponse techniquement réussie ne doit pas être comptée comme un succès.

Dans notre exemple, les consultations admissibles sont celles qui respectent le contrat et les droits d’accès. La définition doit empêcher que les erreurs du système soient retirées du dénominateur simplement parce qu’elles n’ont pas atteint la dernière étape de traitement.

Il faut aussi choisir le point d’observation. La durée mesurée à l’intérieur de l’application ne couvre pas forcément le réseau, l’authentification, l’entrée du service et le retour vers l’utilisateur. Les mesures internes restent utiles pour diagnostiquer ; elles ne remplacent pas automatiquement la mesure de bout en bout.

## 6. Performance : délai, débit et saturation

La latence décrit le temps d’une opération. Le débit décrit un volume traité par unité de temps. La capacité correspond au niveau de charge supportable dans des conditions et pour des objectifs définis. Ces dimensions doivent être étudiées ensemble.

Une moyenne masque la distribution. Une majorité de réponses rapides peut coexister avec des attentes très longues pour certains utilisateurs. Des percentiles comme p95 ou p99 permettent de caractériser une partie de cette distribution, à condition de préciser la fenêtre et la population.

Un p99 n’est pas un maximum. Un résultat satisfaisant pendant un essai court ne démontre pas non plus la stabilité sur une journée. Il faut examiner la durée, les volumes de données, les traitements concurrents et les dépendances externes.

Pour le cas fictif, l’essai de consultation doit inclure les écritures qui se déroulent au même moment. Un test avec une base vide et un partenaire instantané décrit un autre environnement que celui que l’on prétend qualifier.

La surcharge mérite son propre scénario. Le système doit-il refuser certaines demandes, les mettre en attente ou réduire une fonction secondaire ? Le comportement attendu doit être défini avant de constater la saturation.

## 7. Disponibilité : préciser ce qui reste utilisable

La disponibilité doit être rattachée à un service et à des conditions. La consultation peut rester utilisable alors que les nouvelles soumissions sont suspendues. Une disponibilité agrégée de toute l’application peut masquer cette différence.

Deux familles de mesures ne doivent pas être confondues. Une mesure temporelle examine les périodes où le service remplit le critère retenu. Une mesure fondée sur les requêtes examine la proportion d’opérations jugées bonnes. Elles ne produisent pas nécessairement le même résultat lorsque la charge varie.

Dans le cas fictif, nous distinguons disponibilité de réception, de consultation et de confirmation. L’arrêt des notifications ne doit pas être confondu avec une impossibilité de traiter le paiement, si le contrat prévoit leur reprise ultérieure.

Il faut préciser le traitement des maintenances, des périodes sans trafic et des demandes impossibles à observer. Les exclusions relèvent d’un accord explicite, pas d’un ajustement après incident pour améliorer le résultat.

## 8. SLI, SLO et budget d’erreur

Un Service Level Indicator, ou SLI, est un indicateur du niveau de service réellement fourni. Un Service Level Objective, ou SLO, fixe un objectif pour cet indicateur sur une fenêtre donnée. Le Site Reliability Workbook recommande notamment des indicateurs définis comme le rapport entre événements bons et événements totaux. [3]

Pour une consultation, l’indicateur peut être la proportion des demandes admissibles ayant reçu une réponse conforme. Un autre indicateur peut mesurer la proportion servie dans le délai retenu. Il faut décider si la latence et la correction sont combinées ou évaluées séparément.

```text
SLI de succès = consultations conformes / consultations admissibles
SLO = cible définie pour ce SLI sur une fenêtre explicitée
```

Avec un objectif fictif de 99,9 %, le complément représente 0,1 % des événements considérés. Sur un million de demandes admissibles, cela correspond à un budget de mille événements mauvais. Ce budget est fondé sur les requêtes : il ne se convertit pas automatiquement en une durée d’interruption.

Le budget d’erreur sert à orienter les décisions, par exemple l’effort à consacrer à la fiabilité lorsque la marge est consommée. La politique doit être convenue avec les acteurs concernés. Elle ne constitue pas une autorisation d’altérer des données ou d’ignorer une règle métier. [3]

Un engagement contractuel de service, souvent désigné par SLA, doit être examiné dans ses propres termes. Il ne se déduit pas automatiquement du SLO interne.

## 9. Fiabilité, cohérence et exactitude métier

Dans notre application, un résultat correct ne se limite pas à l’absence d’exception technique. Il faut préserver le sens du paiement, ses transitions et la relation avec les faits établis par le partenaire.

L’exigence « pas de double paiement » doit être rendue précise. Parle-t-on de deux messages identiques, de deux demandes représentant la même intention ou de deux effets produits chez un partenaire ? Chacun de ces périmètres demande une analyse différente.

La cohérence doit aussi être contextualisée. Certaines règles exigent une décision immédiate sur un état partagé. Une vue de consultation peut accepter un retard limité, si ce retard ne produit pas une affirmation trompeuse et si l’usage le permet.

Le modèle d’exigences doit donc préciser les situations interdites, les états d’attente autorisés et les mécanismes de rapprochement. Une absence de résultat connu ne peut pas être transformée arbitrairement en succès ou en rejet pour satisfaire une mesure de délai.

## 10. Reprise : distinguer RTO et RPO

Le Recovery Time Objective, ou RTO, exprime l’objectif de temps de rétablissement après une interruption. Le Recovery Point Objective, ou RPO, exprime la perte de données acceptable, mesurée en temps. Ces objectifs orientent la stratégie de reprise et doivent être définis pour un périmètre explicite. [4]

Dans un exercice fictif, un RTO de trente minutes et un RPO de cinq minutes ne signifient pas que tout redémarrage en moins de trente minutes suffit. Il faut définir le début de la mesure et le niveau de service attendu à la fin. Restaurer un serveur ne garantit pas que l’utilisateur retrouve les fonctions nécessaires.

Un RPO ne prouve pas non plus que les systèmes externes sont alignés avec les données restaurées. Le partenaire peut avoir réalisé une action dont la trace locale manque dans le point récupéré. Le rapprochement devient alors une exigence complémentaire.

Un objectif de perte nulle doit toujours être accompagné de son périmètre, du scénario de panne couvert et des mécanismes qui le rendent crédible. Il ne résulte pas d’une simple mention « réplication » sur un schéma.

Enfin, une sauvegarde n’est une preuve de reprise qu’après vérification de sa restauration et des comportements nécessaires au retour du service.

## 11. Sécurité : demander des comportements observables

« Les données doivent être sécurisées » ne décrit pas un contrôle suffisant. Dans le cas fictif, préciser plutôt qu’un utilisateur ne doit consulter que les paiements de son périmètre, qu’une action de reprise exige une autorisation définie et que les données sensibles ne doivent pas apparaître dans les journaux ordinaires.

Ces formulations conduisent à des scénarios : tentative d’accès à un autre périmètre, appel direct contournant l’interface, opération administrative non autorisée et examen des traces produites. Les scénarios devront être complétés par une analyse de risques adaptée ; ils ne constituent pas un référentiel complet de cybersécurité.

Il faut distinguer ce qui doit être empêché, détecté et expliqué après coup. L’authentification d’un utilisateur ne prouve pas qu’il possède le droit d’exécuter chaque opération.

L’exigence doit enfin survivre aux situations d’erreur. Une indisponibilité d’un service d’autorisation ne doit pas conduire implicitement à accorder tous les accès.

## 12. Modifiabilité et testabilité

La qualité ne concerne pas uniquement l’exécution. Une modification peut devenir si coûteuse que l’organisation évite de corriger ou d’améliorer le service.

Un scénario de modifiabilité pourrait demander l’ajout d’une règle d’admissibilité sans modifier les contrats de consultation. Il faut préciser la portée, les conditions et la preuve : modules touchés, tests adaptés, compatibilité vérifiée et temps de réalisation si une mesure de délai est pertinente. Le SEI emploie également des scénarios de qualité pour exprimer la modifiabilité. [2]

La testabilité demande des moyens d’observation et de contrôle. Pour simuler une réponse perdue, l’équipe doit pouvoir reproduire le scénario et retrouver l’état associé. Un système que l’on ne peut pas soumettre à ses échecs importants reste difficile à qualifier.

Il ne s’agit pas d’exiger que tout changement soit gratuit. L’objectif est de distinguer les changements attendus, que la conception doit faciliter, des transformations exceptionnelles qui peuvent justifier un effort plus important.

## 13. Coût et consommation de ressources

Une exigence économique doit préciser la frontière des coûts : infrastructure, licences, transferts, exploitation et effort humain. Réduire une facture d’hébergement ne prouve pas une réduction du coût complet si la charge d’intervention augmente fortement.

Pour notre cas, nous proposons de comparer les options à périmètre et niveau de service équivalents, puis d’examiner le coût par volume utile et le coût de maintien des capacités nécessaires à la reprise.

La consommation de ressources peut être étudiée de la même manière : charge représentative, durée, quantité de travail utile et environnement. Une architecture qui utilise moins de ressources dans une démonstration réduite n’est pas nécessairement plus sobre à service égal.

Les conclusions environnementales demandent une méthode et des données adaptées. Un nombre de conteneurs, un taux CPU ou une dépense financière ne suffit pas, seul, à établir une réduction d’émissions.

## 14. Comprendre les compromis

Une amélioration peut déplacer le coût vers un autre attribut. Un cache peut réduire la latence tout en introduisant un risque de données anciennes. Une réplication peut faciliter certains scénarios de disponibilité tout en ajoutant de la complexité de cohérence. Une séparation en services peut améliorer l’autonomie de livraison et rendre les reprises de bout en bout plus difficiles.

L’architecte doit comparer ces conséquences dans les scénarios concernés. Il ne suffit pas de dire qu’une option est « plus performante » ou « plus résiliente » sans expliquer ce qui est mesuré et ce qui change.

Pour la consultation des paiements, un cache peut être une option si le métier accepte une fraîcheur définie. Si l’utilisateur doit décider d’une action irréversible sur l’état affiché, cette hypothèse doit être réexaminée. Le même mécanisme peut donc être adapté à un usage et inadapté à un autre.

La priorité n’est pas de maximiser tous les attributs, mais d’obtenir les qualités nécessaires sans dégrader les règles indispensables.

## 15. Relier exigences, mécanismes et preuves

Une exigence n’est pas satisfaite parce qu’un pattern correspondant apparaît dans la conception. « Nous utilisons des retries » ne prouve pas la reprise correcte. « Nous avons plusieurs instances » ne prouve pas la continuité. Il faut montrer le lien entre problème, mécanisme et résultat.

| Exigence du cas fictif | Mécanisme à examiner | Preuve attendue |
|---|---|---|
| Consultation dans un délai défini | Chemin de lecture et capacité | Mesure sous charge représentative |
| Répétition sans effet supplémentaire | Identité métier et contrôle concurrent | Tests de doublons, concurrence et redémarrage |
| Reprise après réponse perdue | Référence persistée et interrogation | Incident injecté et rapprochement observé |
| Restauration dans l’objectif convenu | Données, procédures et ressources | Exercice complet chronométré |
| Accès limité au bon périmètre | Autorisation sur le cas d’usage | Tests positifs et négatifs |

Les tests de résilience doivent observer le système sous les adversités visées, et non seulement en fonctionnement normal. Le SEI souligne aussi l’importance d’examiner les données avant, pendant et après la perturbation. [5]

Un résultat doit conserver ses conditions. Le comportement observé pour une panne donnée ne démontre pas automatiquement le comportement pour toutes les autres pannes.

## 16. Raffiner les exigences au fil des observations

Les premiers scénarios révèlent souvent des inconnues : charge réelle, délais du partenaire, volumes de données et durée des reprises. Il faut les traiter comme des questions de connaissance, pas les remplir par des chiffres arbitraires.

Une exigence initiale peut devenir plus précise après une mesure. L’équipe peut également découvrir que la demande est irréaliste avec les contraintes actuelles. La bonne réponse est alors un arbitrage explicite : modifier la contrainte, réduire le périmètre, financer un mécanisme ou revoir l’objectif avec le métier.

Le raffinement ne doit pas devenir une manière d’abaisser silencieusement les critères après un échec. Il faut distinguer correction d’une hypothèse erronée et acceptation d’une qualité moindre.

La démarche par scénarios permet précisément de faire évoluer le contexte et les mesures tout en gardant le besoin intelligible. [1]

## 17. Exercice : corriger trois exigences insuffisantes

Une proposition contient les phrases suivantes : « Le service doit être disponible à 99,99 % », « les données ne doivent jamais être perdues » et « toutes les réponses doivent être rapides ».

Pour chacune, identifier les informations manquantes et proposer un scénario vérifiable. Préciser les décisions qui exigent une validation métier plutôt qu’un choix isolé de l’équipe technique.

Pour la disponibilité, demander le parcours concerné, la définition du succès, la fenêtre, le point d’observation et les exclusions. Pour la conservation, préciser quelles données, quels acquittements, quelles pannes et quels systèmes sont couverts. Pour la performance, définir l’opération, la charge, les volumes et la distribution des délais.

Une correction possible consiste à séparer réception et confirmation, à définir un objectif de consultation sur des demandes admissibles et à construire un exercice de reprise pour un scénario de perte identifié. Il serait incorrect de prétendre que ces formulations garantissent déjà leur réalisation.

Les arbitrages portent notamment sur la fraîcheur acceptable, les modes dégradés, les conséquences d’une perte et les investissements nécessaires. Les mesures éclairent ces choix ; elles ne remplacent pas l’autorité chargée de les prendre.

## 18. La qualité est un contrat de compréhension

Les attributs de qualité deviennent utiles lorsqu’ils relient les attentes des acteurs aux comportements du système. Un bon scénario dit ce qui arrive, dans quelles conditions, ce que l’on attend et comment l’évaluer.

La précision doit porter autant sur le sens que sur les chiffres. Une opération rapide mais incorrecte ne répond pas au besoin. Un service restauré sans ses données utiles ne remplit pas nécessairement son objectif de reprise. Une disponibilité mesurée sur les seules demandes parvenues au serveur peut ignorer une partie de l’expérience réelle.

Ce travail prépare les décisions suivantes : quelles capacités faut-il transformer, quelle cible est cohérente et quelle trajectoire permet de l’atteindre ? Le chapitre suivant abordera ces questions à l’échelle de l’architecture d’entreprise.

## Références

[1] Software Engineering Institute, *Evolutionary Improvements of Quality Attributes: Performance in Practice*, raffinement des scénarios : https://www.sei.cmu.edu/blog/evolutionary-improvements-of-quality-attributes-performance-in-practice/

[2] Software Engineering Institute, *Best Practices in Software Architecture*, six parties d’un scénario et exemples de qualité : https://www.sei.cmu.edu/documents/2913/2006_017_001_22556.pdf

[3] Google, *The Site Reliability Workbook — Implementing SLOs*, indicateurs, objectifs et budgets d’erreur : https://sre.google/workbook/implementing-slos/

[4] Microsoft Learn, *Develop a disaster recovery plan for multi-region deployments*, RTO, RPO et stratégie de reprise : https://learn.microsoft.com/en-us/azure/well-architected/design-guides/disaster-recovery

[5] Software Engineering Institute, *System Resilience Part 6: Verification and Validation*, qualification sous perturbations : https://www.sei.cmu.edu/blog/system-resilience-part-6-verification-and-validation/

[Chapitre précédent](chapitre-03.md) · [Sommaire](../SOMMAIRE.md)
