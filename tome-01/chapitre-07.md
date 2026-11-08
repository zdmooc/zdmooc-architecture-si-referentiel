# Chapitre 7 — Open Agile Architecture, Zachman et IT4IT : des périmètres distincts

## 1. Une même étiquette peut cacher des intentions différentes

Après avoir étudié TOGAF, il est tentant de rechercher un autre cadre qui serait plus complet, plus agile ou plus technique. Cette comparaison n’a de sens que si les objets comparés répondent à la même question.

Open Agile Architecture s’intéresse à l’architecture dans une transformation à la fois numérique et agile. Zachman structure les descriptions nécessaires pour comprendre une entreprise. IT4IT fournit une architecture de référence pour la gestion des produits numériques et de leur cycle de vie. Leurs périmètres se croisent, mais leurs contributions ne sont pas identiques. [1][2][3]

Le cas fictif des paiements va nous aider à les distinguer. Nous souhaitons améliorer la compréhension des résultats, organiser la résolution des exceptions et faire évoluer le service. Il faut donc comprendre le besoin, décrire le système, organiser le travail et maîtriser les informations de réalisation et d’exploitation. Aucun des trois référentiels n’effectue automatiquement toutes ces tâches.

## 2. Trois questions pour se repérer

La première question concerne l’apprentissage et l’organisation : comment transformer les produits et les façons de travailler sans perdre la cohérence architecturale ? O-AA apporte des orientations sur cette articulation.

La deuxième concerne la connaissance : quels aspects faut-il décrire pour ne pas réduire l’entreprise à ses logiciels ou à ses processus ? Zachman fournit une structure de classification.

La troisième concerne la gestion du numérique : comment relier les informations de stratégie, de conception, de réalisation, de déploiement, de consommation et d’exploitation ? IT4IT propose une architecture de référence centrée sur les produits numériques. [1][2][3]

| Approche | Nature et apport principal | Confusion à éviter |
|---|---|---|
| O-AA | Standard d’architecture pour une transformation agile et numérique | Le réduire à une méthode de sprint |
| Zachman | Ontologie et structure de classification | Le présenter comme une suite d’étapes de projet |
| IT4IT | Architecture de référence pour gérer le numérique | Le réduire à un catalogue d’outils |

Cette distinction n’instaure pas une hiérarchie entre les trois. Elle permet de choisir ce que l’on veut en tirer.

## 3. O-AA : relier transformation numérique et transformation agile

Open Agile Architecture, abrégé O-AA, a été présenté par The Open Group comme un standard pour l’architecture dans un environnement agile à l’échelle. Sa présentation associe transformation numérique, organisation, expérience utilisateur, architecture produit et modèle opérationnel s’appuyant sur des plateformes. [1]

La transformation numérique concerne notamment les services, les expériences et les modèles de fonctionnement permis par le numérique. La transformation agile concerne la capacité de l’organisation à apprendre et à adapter ses actions. Les deux ne doivent pas être supposées équivalentes.

Dans notre exemple, ajouter un écran de suivi est un changement de produit. Donner à l’équipe les moyens d’observer les difficultés des clients, d’expérimenter une amélioration et de coordonner les évolutions du partenaire relève aussi des façons de travailler.

Une équipe peut effectuer des livraisons fréquentes sans résoudre le problème utilisateur. Inversement, une cible pertinente peut rester difficile à faire évoluer si toute décision dépend d’une longue chaîne d’approbations. L’intérêt de l’approche est de faire travailler ensemble ces dimensions, pas de promettre qu’une cadence de livraison produira automatiquement de la valeur.

## 4. Partir de l’expérience pour formuler les hypothèses

La présentation d’O-AA insiste sur une architecture et un développement de produits guidés par l’expérience. [1] Pour notre cas, cela invite à observer ce que comprend réellement un utilisateur face à un statut.

Supposons qu’un paiement soit affiché comme « accepté ». Le client peut l’interpréter comme définitivement exécuté alors que l’application signifie seulement que la demande a été reçue. Un test de l’écran peut réussir techniquement tout en laissant subsister cette ambiguïté.

L’équipe formule alors une hypothèse : distinguer réception, attente et résultat établi améliorera la compréhension. Pour l’évaluer, elle doit examiner les messages, les usages et les résultats observés. La conception technique devra permettre cette distinction sans inventer une certitude absente des données.

Ce raisonnement est une application pédagogique à notre situation, non une prescription détaillée d’O-AA. Il montre comment une préoccupation d’expérience peut conduire à revoir le modèle et les contrats, plutôt qu’à modifier seulement l’interface.

L’expérimentation doit rester compatible avec les exigences de sécurité et de correction. On peut tester un libellé ou un parcours avec des données fictives ; on ne doit pas traiter une règle financière essentielle comme une variable à changer sans autorité ni contrôle.

## 5. Autonomie et plateformes : examiner les conditions réelles

O-AA inclut une perspective de modèle opérationnel appuyé sur les plateformes. [1] Pour notre étude, nous en tirons une question de conception : quelles capacités communes permettent à l’équipe produit d’avancer sans reconstruire chaque mécanisme technique ?

Une plateforme peut proposer le déploiement, la gestion de secrets ou l’observation. Il faut cependant préciser ses interfaces, ses responsabilités et les contraintes qu’elle impose. Mettre un outil à disposition ne suffit pas à créer une capacité consommable.

Dans le système de paiements, la plateforme peut faciliter le redémarrage d’une instance et la collecte des traces. L’équipe applicative reste responsable des états persistés et des comportements après reprise, selon les mandats établis. L’autonomie ne signifie donc ni absence de dépendances ni transfert automatique des responsabilités métier à la plateforme.

L’organisation doit également examiner ce qui exige une coordination : contrats externes, données partagées et changements touchant plusieurs consommateurs. Une autonomie annoncée ne devient effective que si les équipes disposent des informations et moyens d’action nécessaires.

## 6. Ce qu’O-AA ne remplace pas

Le standard ne doit pas être pris pour une architecture applicative prédéfinie. Sa présentation vise plusieurs rôles, dont architectes, responsables produit, responsables d’exploitation et ingénieurs logiciels. [1]

Les questions de domaine, de données et de fiabilité restent à résoudre. Adopter une orientation produit ne définit pas l’agrégat Paiement. Introduire une plateforme ne démontre pas l’idempotence d’une commande. Organiser l’apprentissage ne supprime pas la nécessité de décider.

Le chapitre précédent a montré que l’ADM prévoit l’itération et l’adaptation. Il serait donc erroné d’opposer simplement un TOGAF nécessairement figé à un O-AA nécessairement flexible. Dans notre lecture, l’important est de préserver les objectifs, les impacts et les preuves, tout en choisissant une organisation de travail adaptée.

## 7. Zachman : une structure, pas un processus

Zachman International présente son Framework comme une ontologie : une structure des éléments descriptifs essentiels. Le site précise explicitement qu’il ne s’agit pas d’une méthodologie. La structure ne prescrit ni l’ordre du travail, ni son point de départ, ni le compromis entre court et long terme. [2]

Le schéma croise six interrogations de communication et six transformations de l’idée vers sa réalisation. Il est généralement représenté comme une matrice de six par six. [2]

Cette organisation ne correspond pas à trente-six phases à exécuter. Elle sert à distinguer des aspects et des niveaux de description. Transformer chaque cellule en formulaire obligatoire ferait de la classification une procédure qu’elle ne prétend pas être.

L’intérêt pratique est de repérer une description déséquilibrée. Une étude peut détailler les systèmes et les fonctions tout en laissant inconnus les acteurs, les déclencheurs et les motivations. Une autre peut préciser l’objectif sans expliquer les informations indispensables à sa réalisation.

## 8. Les six interrogations dans notre cas

Les interrogations sont What, How, Where, Who, When et Why. [2] Nous les utiliserons comme un guide de lecture, avec des questions propres à notre exemple. Le tableau suivant n’est pas une reproduction de la matrice officielle.

| Interrogation | Lecture pédagogique | Question sur les paiements |
|---|---|---|
| Quoi ? | Informations et éléments significatifs | Quel résultat et quelle référence sont conservés ? |
| Comment ? | Activités et transformations | Comment une situation incertaine est-elle résolue ? |
| Où ? | Localisation et distribution | Où les informations et traitements sont-ils placés ? |
| Qui ? | Acteurs et responsabilités | Qui peut confirmer, expliquer ou reprendre ? |
| Quand ? | Événements et temporalité | Qu’est-ce qui déclenche une recherche de résultat ? |
| Pourquoi ? | Intentions et motivations | Quel objectif justifie le changement ? |

Le même mot peut appeler une réponse différente selon le niveau de description. « Où ? » ne désigne pas toujours une adresse réseau ; à une autre profondeur, il peut concerner la distribution des activités. La convention utilisée doit rester explicite.

La question « quand ? » évite notamment de réduire le temps à un délai HTTP. Elle peut révéler un événement métier, un déclencheur de reprise ou une échéance pertinente pour l’utilisateur.

## 9. Du concept à sa réalisation

Le site officiel nomme les six transformations Identification, Definition, Representation, Specification, Configuration et Instantiation. [2] Nous ne les remplacerons pas par des phases de développement supposées équivalentes.

Pour comprendre leur intérêt, considérons l’idée de référence de paiement. Au niveau de la compréhension métier, elle sert à identifier et à retrouver une demande. Dans une représentation de solution, elle apparaît dans les échanges et les responsabilités de données. Dans une réalisation, elle possède un format, des contraintes, un stockage et des comportements observables.

Ces descriptions doivent correspondre sans être confondues. Définir une colonne technique ne suffit pas à prouver qu’elle porte l’identité attendue. Définir le besoin d’identité ne détermine pas automatiquement le type de colonne.

Cette lecture reste illustrative. Elle ne prétend pas affecter chaque phrase à une cellule normative. La leçon est de conserver la relation entre les expressions abstraites et les réalisations concrètes, en rendant visibles leurs transformations.

## 10. Quand Zachman éclaire une étude — et où il s’arrête

Dans notre projet, le cadre peut aider à examiner les angles morts. L’équipe connaît-elle la fonction sans connaître son responsable ? Décrit-elle la localisation technique sans expliquer la distribution métier ? Présente-t-elle un événement sans motivation claire ?

Cette vérification ne démontre pas la qualité des réponses. Une cellule remplie peut contenir une hypothèse fausse. Une description bien classée peut rester incompatible avec une exigence de disponibilité.

Zachman International distingue précisément la structure de description du processus de transformation. [2] Il faut donc d’autres pratiques pour recueillir les faits, arbitrer les options, organiser les changements et vérifier les résultats.

Le cadre peut être utilisé avec une méthode comme TOGAF sans qu’il soit nécessaire de les confondre. The Open Group rapporte cette complémentarité dans un entretien avec John Zachman : classification et méthode ne jouent pas le même rôle. [4]

## 11. IT4IT : gérer les produits numériques par leurs informations

IT4IT est une architecture de référence pour la gestion des produits et services numériques. La version 3 met notamment en avant le produit numérique et les informations qui relient sa gestion de bout en bout. [3][5]

Le sujet n’est pas l’architecture métier du paiement lui-même, mais aussi l’organisation des capacités qui permettent de concevoir, livrer, fournir et exploiter le service numérique correspondant.

Dans notre cas, on doit pouvoir relier le besoin de suivi à une évolution du produit, cette évolution à une version réalisée, cette version à un déploiement et les incidents à ce qui fonctionne réellement. Sans ces liens, chaque outil peut donner une image partielle et incompatible avec les autres.

IT4IT ne se réduit donc pas à choisir un outil de tickets, une chaîne CI/CD et une supervision. L’architecture de référence conduit à examiner les responsabilités fonctionnelles, les objets d’information et les intégrations qui rendent la chaîne cohérente. [3]

## 12. Les sept chaînes de valeur d’IT4IT

La présentation de la version 3 retient sept value streams : Evaluate, Explore, Integrate, Deploy, Release, Consume et Operate. [3][5] Les expressions françaises ci-dessous sont explicatives ; les noms officiels sont conservés pour éviter une ambiguïté de traduction.

| Value stream | Intention synthétique |
|---|---|
| Evaluate | Évaluer le portefeuille de produits numériques et son alignement stratégique |
| Explore | Explorer les évolutions du produit et leur valeur |
| Integrate | Concevoir et construire les versions prêtes à être déployées |
| Deploy | Déployer une version dans un environnement d’exploitation |
| Release | Fournir les offres de service permettant de rendre le produit disponible à la consommation |
| Consume | Satisfaire la consommation d’une offre par un acteur habilité |
| Operate | Exploiter les instances et maintenir les niveaux de service convenus |

Ces chaînes ne doivent pas être transformées en une cascade où chacune ne commence qu’après l’achèvement définitif de la précédente. Elles décrivent des activités de création de valeur qui concernent des produits et des instances en évolution continue. [3]

Il faut également éviter de mélanger les descriptions de versions. Un lecteur peut rencontrer des documents plus anciens organisés autrement. Les sept chaînes présentées ici correspondent à la perspective de la version 3, également explicitée par son programme de certification. [5]

## 13. Distinguer déployer, proposer et consommer

La distinction entre Deploy, Release et Consume est particulièrement utile. Deploy concerne la mise en place d’une version dans l’environnement. Release concerne la fourniture d’offres de service, avec les conditions de souscription et les interactions de cycle de vie. Consume concerne l’acceptation d’une offre par un acteur habilité et les actions nécessaires à sa fourniture. [3]

Il serait donc réducteur de traduire Release par le seul basculement d’un indicateur de fonctionnalité. Une activation progressive peut être un mécanisme concret, mais la portée de l’offre de service est plus large.

Dans notre exemple pédagogique, le nouveau parcours de suivi peut être déployé sans être encore proposé à tous les utilisateurs. Son ouverture à une population doit être reliée aux droits et aux engagements associés. Sa consommation correspond ensuite à l’accès au service selon l’offre et les habilitations retenues.

Il faut aussi distinguer le sens des demandes. Une demande de paiement relève du métier bancaire ; une demande d’accès à un produit numérique relève de sa consommation. Le fait que les deux traversent des API ne les rend pas identiques.

## 14. Les liens d’information qui rendent l’exploitation compréhensible

Supposons qu’un incident affecte uniquement la nouvelle interprétation d’un statut. L’équipe doit retrouver quelle version réalise cette règle, où elle est déployée, quelles populations utilisent l’offre concernée et quelles dépendances sont impliquées.

Les liens proposés dans notre exemple sont :

```text
Objectif d’amélioration
        ↓
Évolution du produit de suivi
        ↓
Version réalisée
        ↓
Instance déployée
        ↓
Offre et conditions d’accès
        ↓
Usage, mesures et incidents
        └────────→ apprentissage et évolution
```

Cette représentation est une illustration originale, pas une reproduction du modèle d’objets officiel. Elle montre pourquoi l’intégration des informations importe autant que la présence de chaque outil.

Une référence de version absente des données d’exploitation peut rendre un incident difficile à diagnostiquer. Une instance oubliée après une migration peut conserver un coût et un risque. Un ancien contrat d’accès peut laisser des utilisateurs sur un parcours que l’organisation croit retiré.

## 15. Complémentarité sans correspondances forcées

Les trois approches peuvent éclairer un même problème sans se succéder comme des étapes obligatoires.

O-AA aide à examiner comment l’organisation apprend et transforme ses produits. Zachman aide à structurer les descriptions et à distinguer leurs aspects. IT4IT aide à relier les capacités et informations de gestion du numérique. TOGAF peut organiser le travail architectural de transformation abordé au chapitre précédent. [1][2][3][4]

Il n’existe pas de correspondance automatique entre une cellule Zachman, une phase ADM et une chaîne IT4IT. Une phase peut mobiliser plusieurs descriptions. Une description peut être utile à plusieurs décisions. Une chaîne de valeur peut fonctionner pendant plusieurs transformations.

Dans notre cas, la découverte d’un statut incompris peut entraîner une exploration produit, une révision de la description métier et une évolution de version. La même observation circule entre les perspectives ; elle ne change pas de nature parce qu’elle est représentée dans plusieurs cadres.

## 16. Choisir selon le problème à résoudre

Une organisation peut avoir besoin d’un seul apport ciblé plutôt que d’une adoption complète de plusieurs référentiels. La décision doit partir de la difficulté constatée.

| Difficulté de notre cas | Perspective à examiner |
|---|---|
| L’équipe livre vite mais ne vérifie pas la compréhension du client | Expérience, apprentissage et organisation produit avec O-AA |
| Les diagrammes décrivent les fonctions mais ignorent acteurs et déclencheurs | Classification des descriptions avec Zachman |
| Les incidents ne peuvent pas être reliés aux versions et instances | Informations de gestion et intégrations avec IT4IT |
| Plusieurs projets modifient le même périmètre sans cible cohérente | Transformation architecturale et gouvernance avec TOGAF |

Ce tableau propose une orientation de raisonnement, pas une règle imposée par les standards. La première réponse peut être une pratique simple : clarifier un contrat ou relier deux informations déjà présentes. L’objectif n’est pas d’introduire un référentiel supplémentaire lorsque la difficulté est plus limitée.

L’évaluation doit considérer le coût d’apprentissage, la capacité de maintien des descriptions et les responsabilités réelles. Un cadre compris par quelques personnes seulement peut rester difficile à employer au quotidien.

## 17. Exercice : remettre chaque apport à sa place

Une direction souhaite remplir la matrice Zachman pour piloter les sprints, adopter IT4IT pour définir les agrégats Paiement et appliquer O-AA pour éviter tout travail architectural préalable.

Identifier les trois confusions et proposer une articulation plus cohérente.

La matrice de Zachman classe les descriptions ; elle ne fournit pas un pilotage de sprint. IT4IT traite la gestion du numérique et ses informations ; il ne définit pas les invariants du modèle bancaire. O-AA articule architecture et agilité ; sa présentation ne justifie pas la suppression de la réflexion sur les responsabilités et contraintes.

Une proposition mieux fondée organise l’exploration du besoin avec l’équipe produit, vérifie que les descriptions couvrent acteurs, informations et déclencheurs, puis relie la réalisation aux versions et aux instances exploitées. La conception des règles métier reste à approfondir avec les experts du domaine.

Il faut ensuite vérifier les résultats : le client comprend-il mieux le statut, la reprise est-elle maîtrisée et l’exploitation retrouve-t-elle la version en cause ? Le nombre de cadres cités ne constitue pas un résultat.

## 18. Des référentiels pour rendre les questions plus précises

Ce chapitre ne présente pas trois remplaçants de TOGAF. Il distingue trois contributions : organiser la transformation agile et numérique, structurer les descriptions et maîtriser la gestion du produit numérique.

Leur valeur dépend de la manière dont elles améliorent les questions et les décisions. Une classification n’est pas une preuve de cohérence. Une orientation agile ne garantit pas l’apprentissage utile. Une intégration d’outils ne garantit pas le sens des données qu’ils échangent.

Dans le cas des paiements, nous avons encore besoin de comprendre les concepts, le langage et les règles du domaine. Le chapitre suivant abordera le DDD pour examiner ce lien entre la réalité métier et la conception du logiciel.

## Références

[1] The Open Group, *The Open Group Publishes Open Agile Architecture*, présentation fondatrice de 2020, transformation agile et numérique, expérience et plateformes : https://www.opengroup.org/open-group-publishes-open-agile-architecture

[2] Zachman International — FEAC Institute, *About the Zachman Framework*, définition de la structure, interrogations, transformations et distinction avec une méthodologie : https://zachman-feac.com/zachman/about-the-zachman-framework

[3] The Open Group, *The Seven IT4IT Value Streams*, définitions et périmètres des chaînes de valeur : https://digital-portfolio.opengroup.org/it4it-standard/latest/DigitalManagement/seven-it4it-value-streams.html

[4] The Open Group, *A Historical Look at Enterprise Architecture with John Zachman*, distinction entre ontologie et méthode : https://blog.opengroup.org/2015/01/23/a-historical-look-at-enterprise-architecture-with-john-zachman/

[5] The Open Group, *The IT4IT Certification Program*, distinction des corpus 2.1 et 3.0 et chaînes de valeur de la version 3 : https://certification.opengroup.org/docs/datasheets/it4it-exams.pdf

[Chapitre précédent](chapitre-06.md) · [Sommaire](../SOMMAIRE.md)
