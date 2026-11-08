# Chapitre 10 — Design Thinking, agilité à l’échelle et place de SAFe

## 1. Livrer davantage ne signifie pas résoudre le bon problème

L’équipe de notre organisation fictive livre régulièrement des améliorations au suivi des paiements. Les écrans sont plus rapides et les versions plus fréquentes. Pourtant, certains clients continuent à appeler l’assistance parce qu’ils ne savent pas si leur demande a réellement été exécutée.

Ce décalage révèle deux difficultés distinctes. La première concerne la compréhension du besoin : que faut-il améliorer dans l’expérience ? La seconde concerne la réalisation collective : comment plusieurs équipes peuvent-elles construire une réponse cohérente ?

Le Design Thinking aide à explorer les besoins et les possibilités de solution avec les personnes concernées. L’agilité à l’échelle traite notamment la coordination lorsque plusieurs équipes contribuent au même résultat. SAFe propose un cadre structuré pour cette coordination, sans être la seule manière possible de l’organiser.

Ce chapitre examine leurs contributions à l’architecture. Il ne les présente ni comme des styles logiciels ni comme une garantie de performance organisationnelle.

## 2. Désirabilité, faisabilité et viabilité

IDEO présente le Design Thinking comme une approche centrée sur les personnes, qui rapproche les besoins humains, les possibilités techniques et les conditions de réussite de l’organisation. Les trois perspectives sont couramment désignées par désirabilité, faisabilité et viabilité. [1]

Dans notre exemple, une réponse définitive immédiate peut paraître désirable. Elle n’est pas nécessairement réalisable avec le protocole du partenaire. Une solution extrêmement coûteuse à maintenir peut être réalisable sans être viable pour l’organisation.

| Perspective | Question dans notre cas |
|---|---|
| Désirabilité | Le parcours aide-t-il le client à comprendre la situation et à agir ? |
| Faisabilité | Les données et les contrats permettent-ils la réponse proposée ? |
| Viabilité | L’organisation peut-elle financer, exploiter et faire évoluer le service ? |

Ces perspectives ne constituent pas trois validations indépendantes. Une contrainte technique peut amener à reformuler l’expérience. Une observation utilisateur peut révéler qu’un investissement initialement envisagé ne traite pas le vrai problème.

La sécurité et les obligations applicables ne doivent pas être réduites à une préférence parmi d’autres. Elles doivent être intégrées aux contraintes et aux critères de conception, avec les acteurs compétents.

## 3. Observer avant de proposer

Demander aux utilisateurs quelle fonctionnalité ils souhaitent ne suffit pas à comprendre leur difficulté. Il faut examiner leurs situations, leurs décisions et les contournements qu’ils utilisent.

Un client peut demander un bouton « relancer » parce qu’il ne sait pas si sa demande a été reçue. L’assistance peut demander un accès direct aux données parce que l’outil de recherche n’expose pas la référence utile. Dans les deux cas, la solution demandée exprime une difficulté, mais ne doit pas être adoptée sans examen.

Les ressources IDEO mettent en avant l’observation et la compréhension du contexte pour découvrir des besoins et des possibilités de conception. [1][2]

Dans notre étude fictive, l’équipe observe des parcours à partir de données autorisées ou fictives. Elle distingue les mots prononcés, les actions constatées et ses propres interprétations. « L’utilisateur a demandé si le paiement était terminé » est une observation. « Il préfère forcément une notification » est déjà une hypothèse.

Cette discipline protège contre une conception guidée seulement par les préférences de l’équipe.

## 4. Reformuler le problème

La formulation « ajouter une relance » enferme la discussion dans un mécanisme. Une formulation plus ouverte serait : comment permettre au client de comprendre la prise en charge et le dernier résultat connu sans provoquer une nouvelle demande injustifiée ?

Cette reformulation ouvre plusieurs options : améliorer le vocabulaire, afficher une référence, expliquer l’attente, proposer une recherche de résultat ou orienter vers une assistance mieux informée.

Le processus présenté par IDEO alterne exploration des possibilités et sélection de celles à approfondir. Il ne doit pas être interprété comme une progression strictement linéaire. [2]

L’architecte intervient pour examiner les conséquences des idées. Afficher une attente exige un modèle qui la représente. Montrer une référence suppose qu’elle soit disponible et partageable. Promettre un délai impose de savoir si le service peut le tenir.

La reformulation n’est donc pas un exercice de communication détaché de la conception. Elle modifie les questions auxquelles le système devra répondre.

## 5. Prototyper pour apprendre

Un prototype doit être choisi selon l’incertitude étudiée. Une maquette d’écran permet de tester la compréhension d’un statut. Elle ne prouve pas la durabilité d’une référence après incident. Un démonstrateur technique peut explorer la reprise sans démontrer l’utilité du parcours proposé.

| Incertitude | Expérience possible | Ce qu’elle ne prouve pas |
|---|---|---|
| Sens d’un libellé | Observation d’un parcours simulé | Exactitude des données réelles |
| Recherche d’une demande | Prototype d’interrogation du partenaire | Capacité de production complète |
| Reprise après réponse perdue | Essai technique avec perturbation | Compréhension par le client |
| Coût de soutien | Exercice avec l’assistance et l’exploitation | Coût à toute échelle |

IDEO décrit le prototypage et le test comme des moyens d’apprendre et de faire évoluer les idées. [1][2] Leur intérêt n’est pas de produire rapidement un objet à conserver à tout prix.

Les limites doivent être connues des personnes qui observent la démonstration. Un écran utilisant des résultats simulés ne doit pas être présenté comme une preuve que le partenaire fournit ces résultats.

## 6. Relier découverte et réalisation

La découverte du besoin ne doit pas former une phase isolée terminée avant la conception. La réalisation révèle des contraintes ; les usages réels révèlent de nouvelles difficultés. Les deux alimentent le modèle et les priorités.

Dans notre cas, un test du parcours peut montrer que « en cours » reste trop vague. L’équipe découvre ensuite qu’elle peut distinguer une attente de réponse et une recherche active de résultat. Le modèle métier et l’expérience doivent évoluer ensemble.

Inversement, une contrainte ne doit pas être cachée pour préserver une maquette approuvée. Si le partenaire ne fournit pas de confirmation immédiate, il faut revoir le message plutôt que fabriquer une certitude.

Une approche itérative accepte cette révision sans renoncer à la cohérence. Les décisions structurantes et les exigences restent explicites ; elles ne sont pas remplacées par une succession d’improvisations.

## 7. Ce qui change lorsque plusieurs équipes interviennent

Une équipe peut maîtriser son périmètre tout en dépendant d’autres équipes pour un contrat, une donnée ou un environnement. À plus grande échelle, ces dépendances peuvent devenir le principal frein au résultat utile.

Imaginons trois contributions : le canal client, le cycle de vie des paiements et l’intégration au partenaire. Chacune peut terminer ses tâches alors que le parcours complet reste inutilisable, parce que les versions des contrats ou les hypothèses sur les états ne correspondent pas.

```text
Canal client ── dépend du contrat de suivi ──→ Paiements
Paiements ── dépend du sens des résultats ──→ Intégration
Intégration ── dépend du protocole réel ─────→ Partenaire
```

Ajouter des réunions ne supprime pas ces dépendances. Il faut les comprendre, décider lesquelles sont nécessaires et réduire celles qui résultent d’un mauvais découpage.

Le besoin de coordination doit être proportionné. Le simple fait d’avoir plusieurs équipes ne justifie pas automatiquement l’adoption complète d’un cadre à l’échelle.

## 8. La place de SAFe

SAFe fournit des pratiques et des structures de coordination entre équipes, parties prenantes et niveaux de décision. Dans le présent chapitre, nous nous concentrons sur ses mécanismes qui touchent directement l’architecture : planification commune, préparation des capacités techniques et visibilité des travaux nécessaires.

Les pages officielles décrivent notamment un Agile Release Train, ou ART, comme le collectif auquel s’applique la planification commune du PI. Le PI Planning vise à aligner équipes et parties prenantes sur une mission et une vision partagées. [3]

Il ne faut pas en déduire qu’un ART correspond obligatoirement à une application, un contexte DDD ou un ensemble de microservices. Le regroupement du travail et le découpage du logiciel sont liés, mais ne sont pas des catégories identiques.

Dans notre cas fictif, un regroupement pertinent doit permettre d’améliorer le parcours complet. Organiser les équipes uniquement par couche technique peut laisser subsister de longues chaînes de transfert.

## 9. PI Planning : rendre les dépendances discutables

Le PI Planning est une planification collective cadencée. Sa présentation officielle met en avant les objectifs partagés, la capacité disponible et l’identification des dépendances. Elle indique également le rôle de facilitation du Release Train Engineer, ou RTE. [3]

La valeur architecturale n’est pas de transformer chaque tâche en une date certaine. Elle est de faire apparaître ce qui doit être prêt, les hypothèses et les risques qui pourraient empêcher le résultat.

Pour notre parcours de suivi, l’équipe canal dépend d’un contrat exposant la fraîcheur. L’équipe Paiements dépend d’une référence conservée. L’intégration dépend d’une possibilité de recherche chez le partenaire. Ces relations doivent être discutées avant de promettre une ouverture du parcours.

Une dépendance non vérifiée ne devient pas fiable parce qu’elle figure sur un tableau. Il faut décider qui la traite, quelle preuve est attendue et quelle option reste possible si l’hypothèse échoue.

## 10. Architecture intentionnelle et conception émergente

La conception émergente permet d’ajuster les structures à partir de l’expérience de réalisation. L’architecture intentionnelle prépare les orientations et capacités nécessaires lorsque certains impacts dépassent l’équipe locale.

SAFe présente leur équilibre comme un moyen de soutenir la réalisation de solutions complexes. Le texte consacré à l’Architectural Runway souligne les limites d’une émergence seule à grande échelle et la nécessité de coordination sur les fondations communes. [4]

Dans notre exemple, l’équipe peut améliorer l’organisation interne d’un module en réalisant une fonctionnalité. Elle ne doit pas modifier seule le sens d’un statut utilisé par plusieurs consommateurs. Ce contrat nécessite une intention explicite et une coordination.

À l’inverse, vouloir fixer toutes les classes et tous les paramètres plusieurs mois à l’avance rendrait l’architecture fragile face aux apprentissages. Le bon équilibre dépend de l’impact, de l’incertitude et du coût de modification.

## 11. Comprendre l’Architectural Runway

L’Architectural Runway correspond aux fondations déjà disponibles dans le code, les composants et l’infrastructure pour faciliter les fonctionnalités proches. [4] Il ne désigne donc pas seulement une vision ou une liste de travaux futurs.

Dans notre cas, la conservation d’une référence stable, un contrat de suivi compris et un mécanisme de test des réponses perdues peuvent préparer plusieurs évolutions. Tant que ces capacités n’existent que sur un schéma, elles ne fournissent pas l’appui attendu à la réalisation.

Il ne faut pas pour autant construire un socle immense en anticipant tous les besoins possibles. La préparation doit être reliée à des fonctionnalités et risques identifiés.

La différence peut être formulée simplement : une idée d’architecture indique une direction ; un travail planifié prépare sa réalisation ; une capacité réalisée et utilisable constitue une fondation disponible. Les trois sont nécessaires à certains moments, mais ne doivent pas être confondus.

## 12. Rendre les enablers visibles

SAFe appelle enablers des éléments de backlog qui soutiennent notamment l’exploration, l’évolution architecturale, l’infrastructure ou les travaux de conformité. Ils sont gérés dans les backlogs appropriés et doivent produire des résultats tangibles. [5]

La traduction pédagogique « travaux facilitateurs » peut aider, mais le terme officiel sera conservé. Il ne doit pas devenir une catégorie opaque dans laquelle ranger tout ce que le métier ne comprend pas immédiatement.

| Travail proposé dans notre cas | Résultat attendu |
|---|---|
| Vérifier la recherche chez le partenaire | Contrat et comportement établis dans les situations étudiées |
| Préparer le contrôle concurrent | Modification d’état protégée et testée |
| Mettre en place un environnement de reprise | Scénarios d’arrêt reproductibles |
| Vérifier une règle d’accès | Preuves des autorisations et des refus |

Le lien avec la valeur doit être expliqué. Un environnement de reprise est utile parce qu’il permet de qualifier des comportements nécessaires, non parce qu’un environnement supplémentaire serait une fin en soi.

Une dette technique n’est pas automatiquement prioritaire, et une fonctionnalité visible ne doit pas automatiquement passer devant toute préparation. Les conséquences du report doivent être comparées.

## 13. Le rôle des architectes dans cette coordination

L’architecte relie la vision et les contraintes aux décisions concrètes. Il prépare les sujets qui dépassent une équipe, vérifie les hypothèses risquées et aide à séparer ce qui doit être commun de ce qui peut rester local.

Il ne remplace ni les responsabilités produit ni la capacité des équipes à concevoir. Dans notre proposition d’organisation, il participe aux discussions tôt et revient sur les résultats réels, plutôt que d’intervenir seulement comme approbateur final.

Les spécialistes de sécurité, de données et de plateforme doivent être intégrés selon les impacts. Ils ne doivent pas constituer une succession de guichets où un choix figé est présenté trop tard.

La répartition précise dépend des mandats de l’organisation. Les titres de rôles d’un cadre ne suffisent pas à déterminer qui peut accepter un risque ou arbitrer une règle métier, comme nous l’avons vu au chapitre 3.

## 14. Concevoir des incréments utilisables

Un incrément utile ne se réduit pas à une couche terminée. Une API disponible sans donnée pertinente, un écran sans contrat stable ou un traitement sans reprise peuvent rester inutilisables pour le parcours.

Pour notre étude, une première tranche pourrait permettre à une population autorisée de consulter un paiement avec son dernier état connu et une fraîcheur expliquée. Elle nécessiterait une partie du canal, de l’application, des données et des contrôles.

Une tranche suivante pourrait outiller la recherche d’un résultat inconnu, avec les droits et observations nécessaires. Le périmètre reste limité, mais le comportement doit être cohérent.

```text
Besoin limité
    ↓
Contrat et règle compris
    ↓
Réalisation traversant les composants nécessaires
    ↓
Vérification du parcours et de ses échecs pertinents
    ↓
Observation et décision d’évolution
```

Le découpage réduit la portée ; il ne dispense pas des propriétés indispensables dans cette portée. Le qualificatif « minimal » ne justifie pas une action financière non maîtrisée.

## 15. Distinguer déploiement, ouverture et résultat

Une version déployée n’est pas nécessairement proposée à tous les utilisateurs. Une fonctionnalité proposée n’a pas nécessairement produit le bénéfice attendu.

Cette distinction rejoint celle d’IT4IT étudiée au chapitre 7, même si les vocabulaires ne doivent pas être assimilés terme à terme. Dans notre cas, une évolution peut être installée, ouverte à une population limitée, puis observée avant un élargissement.

L’ouverture progressive doit préciser les populations, les données, les dépendances et la stratégie en cas de problème. Revenir à une version précédente ne signifie pas pouvoir annuler tous les effets métier déjà produits.

Il faut également distinguer la démonstration d’un parcours et la preuve de sa robustesse. Une présentation nominale ne remplace pas un test de concurrence ou un exercice de reprise. Le rythme de livraison n’efface pas cette différence.

## 16. Mesurer les résultats et la capacité d’apprendre

Compter les fonctionnalités terminées renseigne sur la production, mais pas directement sur l’amélioration du service. Pour notre cas, il faut observer la compréhension des états, le délai de clarification, les demandes d’assistance et les opérations restant sans résultat expliqué.

Ces mesures doivent être interprétées. Une diminution des appels peut venir d’une meilleure compréhension ou d’un accès à l’assistance devenu plus difficile. Il faut confronter les chiffres aux usages.

L’organisation peut aussi examiner ses délais d’attente : attente de contrat, d’environnement, de revue ou de décision. Un grand nombre d’équipes occupées peut coexister avec un parcours bloqué entre leurs frontières.

La découverte et la coordination doivent conduire à des décisions : poursuivre, corriger, réduire ou abandonner une option. Un apprentissage qui n’influence jamais le produit ou l’architecture reste peu utile.

## 17. Les dérives à éviter

Le Design Thinking peut devenir une séance de production d’idées sans rencontre avec les utilisateurs ni retour sur les hypothèses. Le prototype devient alors un objet séduisant que l’on cherche à défendre plutôt qu’un moyen d’apprendre.

L’agilité à l’échelle peut devenir une synchronisation de tâches sans réduction des dépendances. Les équipes s’engagent sur des résultats qui reposent sur des hypothèses non vérifiées, tandis que les difficultés de fond restent inchangées.

L’architecture peut également prendre deux formes excessives : une conception détaillée qui refuse les retours de réalisation, ou une absence d’orientation qui laisse chaque équipe réinventer les contrats partagés.

Ces dérives ne sont pas corrigées par l’adoption d’un vocabulaire. Le critère reste le comportement collectif obtenu : meilleure connaissance du besoin, décisions explicites, réalisation cohérente et preuves adaptées.

## 18. Exercice : livrer un écran ou améliorer le suivi ?

Trois équipes annoncent avoir terminé leurs tâches : le canal a livré le nouvel écran, Paiements a livré un statut et l’intégration a réalisé l’appel partenaire. Le client ne comprend toujours pas si sa demande a été exécutée. La reprise n’a pas été testée.

Proposer une démarche combinant exploration du besoin, coordination et préparation architecturale. Identifier un prototype, un travail facilitateur et un incrément utile.

Une réponse argumentée observe d’abord la compréhension de l’utilisateur et clarifie le sens du statut. Une maquette permet d’examiner les messages sans prétendre prouver l’exécution. Un essai de réponse perdue établit les informations accessibles et les conditions de reprise.

Le contrat commun, la persistance de la référence et l’environnement d’essai peuvent faire l’objet de travaux visibles. L’incrément réunit ensuite le parcours limité avec les accès, les données et les scénarios d’erreur nécessaires.

SAFe peut aider à coordonner ce travail si l’organisation l’a retenu. La correction ne suppose pas que ce cadre soit obligatoire : elle exige surtout de relier les contributions au résultat de bout en bout.

## 19. Explorer ensemble, construire avec cohérence

Design Thinking, agilité à l’échelle et architecture répondent à des préoccupations complémentaires. L’exploration cherche à comprendre le problème et les options utiles. La coordination rend les dépendances et la capacité collective discutables. L’architecture relie les décisions, les contraintes et les mécanismes nécessaires.

SAFe propose des moyens pour organiser une partie de cette coordination, préparer les fondations techniques et rendre les travaux facilitateurs visibles. Son adoption ne garantit ni la pertinence du produit ni la qualité du découpage.

Dans notre cas, la réussite ne se lit pas dans le nombre de sprints ou de démonstrations. Elle se lit dans un suivi compréhensible et correctement réalisé. Le chapitre suivant approfondira les langages de modélisation qui permettent de représenter les processus, les structures et leurs relations.

## Références

[1] IDEO, *Design Thinking by IDEO*, approche centrée sur les personnes et articulation désirabilité-faisabilité-viabilité : https://designthinking.ideo.com/

[2] IDEO, *The design thinking process*, exploration, convergence, prototypage, test et itération : https://designthinking.ideo.com/process

[3] Scaled Agile, *PI Planning*, alignement de l’ART, objectifs, capacité et dépendances : https://framework.scaledagile.com/pi-planning/

[4] Scaled Agile, *Architectural Runway*, articulation entre conception émergente, architecture intentionnelle et fondations disponibles : https://framework.scaledagile.com/architectural-runway/

[5] Scaled Agile, *Enablers*, travaux d’exploration, d’architecture, d’infrastructure et de conformité : https://framework.scaledagile.com/enablers/

[Chapitre précédent](chapitre-09.md) · [Sommaire](../SOMMAIRE.md)
