# Chapitre 2 — DDD stratégique : langage, sous-domaines et contextes

## 1. Découper les modèles avant de distribuer les applications

Le chapitre précédent a délimité une solution et ses engagements. Il reste à décider quels modèles permettront de concevoir ses responsabilités. Une frontière de projet ne suffit pas : le même projet peut traverser plusieurs domaines, tandis qu’une application existante peut mélanger plusieurs façons de comprendre le métier.

La conception stratégique du Domain-Driven Design examine les parties du domaine, la portée des modèles et les relations entre leurs responsables. Elle prépare les choix de réalisation sans les réduire au nombre de services.

Le DDD Reference d’Eric Evans distingue notamment la cartographie des contextes et la concentration de l’effort sur le cœur de domaine. Microsoft présente également l’analyse du domaine comme un travail itératif, sans procédure mécanique produisant les bonnes frontières. [1][2]

Nous allons construire une première proposition pour le système fictif de paiements, puis l’éprouver avec des situations et des contraintes. Le découpage obtenu sera une hypothèse argumentée, non un standard bancaire.

## 2. Partir des décisions métier

Une liste de noms comme Client, Compte et Paiement ne suffit pas à découvrir les responsabilités. Ces objets peuvent apparaître dans plusieurs activités sans y avoir le même sens.

Commençons par les décisions : accepter une demande dans le périmètre local, déterminer les conditions d’une soumission, interpréter un résultat externe, organiser une recherche ou choisir le canal d’une notification.

Pour chacune, nous devons comprendre qui peut décider, quelles connaissances sont nécessaires et quels faits changent la situation. Une règle de délai de notification et une règle de reconnaissance du résultat ne mobilisent pas nécessairement le même modèle.

| Décision du cas fictif | Connaissance nécessaire |
|---|---|
| Autoriser une soumission | Règles locales et état de la demande |
| Reconnaître une confirmation | Sens du contrat et faits reçus |
| Ouvrir une recherche | Résultat connu et possibilités de résolution |
| Déclencher une notification | Fait à communiquer et préférences de livraison |

Ces regroupements demandent encore à être discutés. L’analyse ne doit pas transformer immédiatement chaque ligne en service.

## 3. Repérer les sous-domaines

Un sous-domaine représente une partie du problème métier. Sa compréhension exige des concepts, des règles et des responsabilités qui lui sont propres.

Dans notre étude, nous pouvons examiner la conduite des demandes, la résolution des situations incertaines, la communication avec les utilisateurs et les usages comptables. Ce sont d’abord des sujets d’analyse, pas des noms de composants à créer.

Microsoft distingue les fonctions proches, leur importance pour l’activité et leurs dépendances avant d’identifier les contextes et les réalisations. L’article insiste également sur l’identification précoce des systèmes externes sans commencer par leurs technologies. [2]

Une fonction secondaire dans un parcours peut être importante dans une exception. La résolution d’un résultat tardif peut mobiliser peu d’opérations mais demander une compréhension spécialisée et des responsabilités fortes.

Il faut donc éviter de confondre volume, visibilité et valeur stratégique.

## 4. Cœur, support et générique

La distinction entre sous-domaines de cœur, de support et génériques aide à affecter l’effort de conception. Le cœur contribue à la différenciation ; le support répond à un besoin propre sans constituer cette différenciation ; le générique concerne des problèmes pouvant être satisfaits par des solutions courantes. [2]

Dans notre cas, l’organisation pourrait considérer son traitement des exceptions comme une aptitude différenciante. Cette qualification doit venir de sa stratégie, pas de l’opinion isolée de l’équipe technique.

Une fonction générique n’est pas une fonction négligeable. Elle peut être indispensable à la sécurité ou à la disponibilité. De même, une activité centrale pour les opérations n’est pas automatiquement un avantage concurrentiel.

La classification oriente une question pratique : où l’organisation doit-elle investir dans un modèle spécifique, et où peut-elle réutiliser une solution en maîtrisant son contrat ? Evans propose précisément de distinguer le cœur et d’éviter que l’effort essentiel soit dilué dans les autres préoccupations. [1]

La réponse peut évoluer avec le métier. Une décision ancienne de réutilisation mérite d’être revue si les règles deviennent réellement spécifiques.

## 5. Éprouver le vocabulaire dans des phrases

Le langage partagé ne se construit pas seulement avec une colonne de définitions. Il doit permettre de raconter les situations et de discuter des règles.

Prenons la phrase « le paiement est accepté ». Demandons qui l’affirme, à quel moment et pour quel effet. L’application peut avoir accepté la forme de la demande ; le partenaire peut avoir reconnu sa réception ; le métier peut parler d’une autorisation. Employer le même terme ne rend pas ces faits équivalents.

Le DDD Reference demande une cohérence du langage à l’intérieur du contexte et relie son évolution à celle du modèle et du code. [1]

Pour notre atelier, nous proposons de confronter chaque terme à un exemple et à un contre-exemple :

| Terme | Exemple proposé | Contre-exemple utile |
|---|---|---|
| Demande reçue | Prise en charge selon le contrat local | Simple ouverture de l’écran |
| Résultat reconnu | Fait interprétable selon le contrat | Réponse technique sans sens établi |
| Recherche ouverte | Résolution engagée sur une référence | Nouvelle intention de paiement |

Si le contre-exemple fait hésiter les experts, le modèle doit être approfondi avant d’être figé dans les interfaces.

## 6. Accepter plusieurs modèles légitimes

Le même élément réel peut être représenté différemment selon le problème. Ce n’est pas nécessairement une duplication à supprimer.

Dans notre cas, la notification peut connaître un identifiant de paiement et un message à délivrer sans représenter les règles d’autorisation de soumission. La comptabilité peut utiliser des faits établis sans exposer le même cycle de consultation au client.

Microsoft illustre cette idée en montrant qu’un même objet peut nécessiter des propriétés très différentes selon l’usage. Construire un modèle unique pour tous peut augmenter les contraintes de coordination et la difficulté d’évolution. [2]

Dans un exemple d’assurance fictif, le terme « client » peut désigner le souscripteur dans un modèle contractuel et le destinataire d’une information dans un modèle de communication. Ces usages doivent être validés pour le cas étudié ; ils n’imposent pas une classe universelle Client.

Partager un identifiant peut faciliter la correspondance sans obliger à partager toutes les règles ou la représentation interne.

## 7. Définir le contexte délimité

Un bounded context fixe où un modèle est défini et applicable. Cette limite doit être explicite et se traduire dans l’organisation du travail et dans la réalisation. [1]

Un contexte candidat doit pouvoir être décrit par sa finalité, son langage, ses décisions, ses informations et ses relations. Une simple étiquette comme « finance » ne suffit pas.

Pour notre étude, une proposition pourrait être :

| Contexte candidat | Finalité | Ce qu’il ne décide pas |
|---|---|---|
| Paiements | Conduire la demande et reconnaître son résultat | Organisation interne du partenaire |
| Résolution | Organiser l’investigation d’une situation | Réécriture arbitraire des faits |
| Notifications | Livrer une information à un destinataire | Validité métier de l’exécution |

La séparation Paiements/Résolution reste à éprouver. Si chaque règle de résolution nécessite de connaître intimement les états internes du paiement, les frontières peuvent être trop fines ou le contrat insuffisant.

Un contexte ne se définit donc pas en ajoutant un dossier au dépôt. Sa cohérence doit pouvoir être expliquée et entretenue.

## 8. Maintenir la cohérence interne

Délimiter le contexte ne suffit pas à préserver son modèle. Les personnes peuvent continuer à employer des termes différents ou introduire des interprétations contradictoires dans le code.

Evans associe la cohérence interne à l’intégration fréquente des réalisations, aux tests et à l’usage continu du langage partagé. Il met aussi en garde contre une fragmentation en contextes toujours plus petits qui ferait perdre une cohérence utile. [1]

Dans notre cas, deux traitements d’entrée ne doivent pas reconnaître différemment une confirmation sans raison explicite. La variation peut être légitime si les contrats diffèrent, mais le modèle doit alors la représenter.

Les exemples métier, les tests de règles et les revues de vocabulaire constituent des moyens complémentaires. La réussite de la compilation ne prouve pas l’unité du modèle.

Une équipe qui découvre des contradictions doit pouvoir corriger le langage et la réalisation, pas seulement ajouter une condition supplémentaire à chaque exception.

## 9. Cartographier les relations réelles

Une context map décrit les contextes et leurs relations. Elle doit rendre visibles les dépendances de modèle et les conditions de collaboration, pas seulement les flux réseau. [1]

L’amont fournit un modèle, des informations ou un service dont l’aval dépend. Cette position ne se déduit pas du sens d’une requête HTTP ni du niveau hiérarchique des équipes.

Dans notre exemple, le partenaire fournit un contrat que nous devons interpréter. Paiements publie des faits consommés par Notifications. L’acteur qui appelle techniquement en premier n’est pas nécessairement l’amont dans le sens de la relation de modèle.

```text
[Partenaire : amont]
        │ contrat externe
        ▼
[Paiements : aval protégé par traduction]
        │ contrat publié ; Paiements est ici amont
        ▼
[Notifications : aval]
```

La légende est indispensable : les flèches indiquent ici une dépendance au contrat fourni, non un diagramme de tous les échanges.

La carte doit commencer par la situation effective. Une relation cible souhaitée ne doit pas masquer une absence réelle de capacité de négociation.

## 10. Partenariat et relation client-fournisseur

Dans un partenariat, deux contextes ont un besoin suffisamment lié pour organiser ensemble leur évolution. Cela demande de la coordination et des engagements réciproques. Ce n’est pas seulement le fait d’utiliser la même plateforme. [1]

Dans une relation Customer/Supplier Development, les besoins de l’aval sont pris en compte dans la planification et la collaboration avec l’amont. La différence importante est la possibilité réelle de négocier et de vérifier l’engagement. [1]

Pour notre cas, un fournisseur d’états partagé entre plusieurs produits peut prévoir des échanges sur les évolutions nécessaires aux consommateurs. Si aucune demande de l’aval n’est prise en compte, qualifier la relation de client-fournisseur au sens DDD ne suffit pas à la rendre telle.

Il faut expliciter les sujets communs : sens des résultats, compatibilité, exemples d’erreur et conditions de changement. Un document signé sans mécanisme de collaboration peut laisser les dépendances intactes.

## 11. Conformist et couche anticorruption

L’aval peut choisir d’adopter le modèle de l’amont. Le pattern Conformist reconnaît cette situation plutôt que de financer une traduction dont le bénéfice ne serait pas suffisant. [1]

Cette option peut convenir à un besoin générique. Elle peut devenir coûteuse si elle force le cœur métier à employer des concepts mal adaptés.

L’Anticorruption Layer protège au contraire le modèle local par une traduction. Elle porte les différences de signification, pas seulement la conversion de types ou de formats. [1]

Dans notre intégration fictive, un code partenaire signifiant « demande enregistrée » ne doit pas être transformé en « exécution confirmée ». La traduction peut produire un fait plus limité, préserver l’information d’origine ou orienter une situation non interprétable vers un traitement prévu.

Une couche anticorruption ne corrige pas magiquement une source ambiguë. Elle doit pouvoir exprimer les limites du contrat et refuser de fabriquer un sens absent.

## 12. Shared Kernel : partager peu et consciemment

Un Shared Kernel est une portion explicitement partagée du modèle et de sa réalisation, dont les changements demandent une coordination entre les parties concernées. [1]

Ce n’est pas toute bibliothèque commune. Un utilitaire de journalisation ne constitue pas automatiquement un noyau métier partagé. À l’inverse, partager toutes les classes du domaine dans un module commun peut créer un couplage bien plus large que prévu.

Dans notre étude, un petit concept monétaire pourrait être partagé si sa sémantique, ses contraintes et son évolution conviennent réellement aux contextes concernés. Cette option doit être confrontée aux différences d’arrondi, de précision et d’usage ; le nom Montant ne démontre pas l’identité du modèle.

Le partage implique un coût de synchronisation. Il faut préciser ce qui est commun, comment les évolutions sont décidées et comment leurs effets sont vérifiés.

## 13. Open Host Service et Published Language

Un Open Host Service expose un protocole ou un service d’intégration utilisable par plusieurs consommateurs. Un Published Language fournit un langage documenté et partagé pour exprimer les échanges. Ces patterns peuvent se compléter. [1]

Un contrat JSON syntaxiquement valide ne suffit pas à constituer une sémantique partagée. Les états, les identifiants, les unités et les règles d’évolution doivent être compréhensibles.

Pour notre contexte Paiements, un fait publié pourrait préciser l’identité de la demande, la nature du résultat reconnu et la référence nécessaire à la correspondance. Il n’a pas à exposer toutes les structures internes de l’agrégat.

Ces patterns ne dépendent pas exclusivement de REST. Un mécanisme de messages peut également transporter un langage publié. Le choix technique doit être traité avec les contraintes de livraison, de sécurité et de reprise.

## 14. Separate Ways : choisir de ne pas intégrer

L’intégration possède un coût. Separate Ways reconnaît qu’une absence de collaboration logicielle peut être préférable lorsque le bénéfice ne justifie pas ce coût. [1]

Dans notre exemple, deux fonctions de présentation sans règle commune peuvent conserver des réalisations distinctes. Cette option serait beaucoup moins crédible si elles doivent maintenir ensemble une autorité unique sur le résultat.

Il faut distinguer indépendance et duplication dangereuse. Reproduire un calcul métier essentiel dans deux contextes sans stratégie de cohérence peut créer une divergence, même si l’absence d’échange semble simplifier le déploiement.

La décision de ne pas intégrer doit donc expliquer les conséquences acceptées. Elle n’est ni une invitation à ignorer les dépendances existantes ni un synonyme d’isolation réseau.

## 15. Organiser un atelier de découverte

La méthode proposée ici utilise des situations concrètes sans imposer un formalisme particulier. Microsoft souligne que l’analyse peut partir d’un tableau partagé ou d’approches plus structurées ; l’objectif est la compréhension commune avant les choix technologiques. [2]

L’atelier commence par une demande complète et ses variantes. Les participants identifient les actions, les faits significatifs, les décisions et les informations nécessaires. Ils marquent les termes ambigus au lieu de les normaliser trop vite.

Les regroupements apparaissent ensuite : règles qui changent ensemble, concepts cohérents et parties pouvant évoluer sans connaître les détails des autres. Les dépendances et les autorités sont discutées séparément des noms de services.

La proposition de contexte est éprouvée sur une évolution. Si ajouter une préférence de notification oblige à modifier les règles de confirmation, il faut comprendre pourquoi. Si changer le protocole partenaire traverse tout le domaine, la protection du modèle est peut-être insuffisante.

Ce sont des indices, pas un algorithme automatique de découpage.

## 16. Éprouver la carte avec un changement

Supposons que l’organisation ajoute un nouveau partenaire dont les résultats utilisent un vocabulaire différent. Le contexte Paiements doit-il changer toutes ses règles, ou une traduction peut-elle préserver le modèle local ?

Une traduction n’est adaptée que si les faits fournis correspondent aux besoins. Si le nouveau contrat introduit une réalité métier différente, le modèle lui-même peut devoir évoluer. L’objectif n’est pas de conserver une abstraction devenue fausse.

Examinons ensuite la séparation de Résolution. Si elle peut gérer une investigation à partir d’un contrat clair sans modifier librement les paiements, elle peut constituer un contexte cohérent. Si elle dépend constamment de leurs détails internes, il faut réexaminer la frontière.

Microsoft rappelle que l’évaluation des frontières est continue et qu’une évolution du système peut conduire à les redéfinir. [2]

La carte stratégique est un outil d’apprentissage et de coordination, pas une cible à défendre indépendamment des observations.

## 17. De la carte au code et au déploiement

Une frontière de modèle doit se traduire dans les dépendances et les interfaces. Dans une application modulaire, des packages et contrats explicites peuvent préserver plusieurs contextes. Dans une solution distribuée, des contrats distants ajoutent des contraintes opérationnelles.

Il ne faut pas déployer séparément tous les contextes par automatisme. Le choix dépend aussi des équipes, des exigences de qualité, des données et du coût de distribution. L’analyse du domaine contribue à ces décisions sans les épuiser. [2]

Une bibliothèque d’objets partagée par tous les contextes peut contredire l’autonomie annoncée. Une base commune accessible sans règle peut contourner leurs responsabilités. Les frontières doivent donc être vérifiées dans les chemins réels d’accès, pas seulement dans une carte.

Le chapitre suivant abordera les agrégats et leurs invariants. Le contexte fournit une portée de modèle ; l’agrégat précisera une frontière de cohérence à l’intérieur de cette portée. Ces deux frontières ne sont pas interchangeables.

## 18. Exercice : le modèle Client universel

Une organisation fictive veut imposer une classe Client unique aux paiements, aux notifications et aux déclarations de sinistre. Chaque équipe doit y ajouter ses champs. Les versions doivent être livrées ensemble pour éviter les incompatibilités.

Identifier les questions de conception stratégique avant de proposer un remplacement.

Une réponse argumentée examine les sens du terme dans chaque usage, les identifiants nécessaires, les décisions prises et la capacité de chaque équipe à influencer les évolutions. Elle distingue les informations vraiment communes des règles propres à chaque modèle.

Un langage d’échange peut permettre de partager certaines références sans partager toutes les classes internes. Un petit noyau commun peut rester une option si sa sémantique et sa gouvernance sont maîtrisées. Il n’est pas nécessaire de choisir d’avance entre partage total et absence d’intégration.

La correction doit également examiner les autorités de données et les effets sur les consommateurs. Remplacer une bibliothèque commune par des copies non contrôlées ne résout pas automatiquement le problème.

## 19. Passer de frontières supposées à des modèles explicites

Le DDD stratégique relie l’effort de conception à la valeur métier et reconnaît la pluralité des modèles. Les sous-domaines décrivent les problèmes ; les contextes définissent où les modèles sont applicables ; la carte exprime leurs relations et la manière dont les équipes peuvent collaborer.

Le langage doit vivre dans les scénarios et le logiciel. Les patterns de relation permettent d’assumer les dépendances au lieu de les masquer. Leur choix doit expliquer ce qui est partagé, traduit, négocié ou laissé indépendant.

Une stratégie utile ne se juge pas au nombre de contextes. Elle se juge à la cohérence des décisions qu’ils rendent possibles et aux effets de leurs frontières sur les évolutions. Le chapitre suivant passera à la conception tactique : entités, objets-valeur, agrégats et protection des invariants.

## Références

[1] Eric Evans, *Domain-Driven Design Reference — Definitions and Pattern Summaries*, Domain Language, 2015 ; définitions, Bounded Context, Ubiquitous Language, Continuous Integration, Context Map, patterns de relation et Core Domain : https://www.domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf

[2] Microsoft Learn, *Use domain analysis to model microservices*, sous-domaines, cohésion, langage, frontières et analyse itérative ; le contexte microservices de la source ne rend pas ce déploiement obligatoire dans notre étude : https://learn.microsoft.com/en-us/azure/architecture/microservices/model/domain-analysis

[Chapitre précédent](chapitre-01.md) · [Sommaire](../SOMMAIRE.md)
