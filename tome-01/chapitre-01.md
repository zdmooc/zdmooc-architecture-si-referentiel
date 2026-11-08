# Chapitre 1 — De l’objectif métier à la décision d’architecture

## 1. Une demande n’est pas encore une architecture

« Nous devons moderniser l’application de paiements. » La phrase semble claire. Elle ne l’est pas suffisamment pour concevoir une solution. Moderniser peut signifier réduire le coût d’exploitation, corriger une fragilité, accélérer les changements ou remplacer une dépendance devenue problématique. Ces motivations ne conduisent pas nécessairement aux mêmes décisions.

L’une des premières responsabilités de l’architecte consiste donc à transformer une intention en un problème explicite. Il ne s’agit pas de retarder l’action, mais d’éviter de résoudre avec précision un problème mal choisi.

Dans notre cas fictif, les utilisateurs se plaignent de ne pas connaître l’état de certains paiements. L’assistance doit consulter plusieurs outils. Les équipes proposent de remplacer l’application par des microservices. Cette proposition est une option technique, non la définition du problème. La première question reste : pourquoi le statut n’est-il pas fiable ou accessible ?

Peut-être les règles de transition sont-elles ambiguës. Peut-être les réponses du système externe ne sont-elles pas rapprochées correctement. Peut-être l’information existe-t-elle mais n’est-elle pas exposée au bon acteur. Une refonte technique ne constitue pas, à elle seule, une réponse à ces trois situations.

## 2. Distinguer objectif, exigence, contrainte et solution

Un objectif décrit un résultat recherché : réduire les demandes d’assistance liées aux statuts incompréhensibles. Une exigence précise un comportement attendu : permettre à un utilisateur autorisé de consulter le dernier état connu et d’en comprendre la signification. Une contrainte limite les options : conserver une interface externe pendant la première étape de migration. Une solution propose des mécanismes : construire une vue de suivi et un processus de rapprochement.

Cette distinction protège le raisonnement. Si la solution est imposée avant l’analyse, les exigences risquent d’être reformulées pour justifier un choix déjà fait.

| Formulation | Nature | Travail à poursuivre |
|---|---|---|
| Simplifier le suivi des paiements | Objectif | Définir le bénéfice et sa mesure |
| Afficher le dernier état connu | Exigence | Préciser fraîcheur et signification |
| Garder l’interface partenaire | Contrainte | Examiner ses garanties et ses limites |
| Créer un service de suivi | Option de solution | Comparer avec les alternatives |

La même prudence s’applique à « passer au cloud » ou « adopter Kubernetes ». Ces choix peuvent répondre à des besoins légitimes, mais le besoin ne doit pas disparaître derrière le nom de la technologie.

## 3. Identifier les préoccupations des acteurs

Le client veut savoir si sa demande a été prise en compte. L’assistance veut expliquer le résultat. L’exploitation veut détecter une accumulation d’opérations en attente. La sécurité veut contrôler qui peut consulter et agir. L’équipe de développement veut faire évoluer les règles sans casser les usages existants.

Ces préoccupations se recouvrent sans être identiques. Les réunir dans un seul mot, « fiabilité », masque les arbitrages. Une interface peut être disponible alors que ses informations sont en retard ; une réponse peut être rapide sans apporter de certitude sur le résultat métier.

L’ADM de TOGAF demande notamment de clarifier les objectifs, les moteurs de changement, les contraintes et les risques de transformation. L’intérêt de ce cadrage est de donner un contexte aux choix, plutôt que de commencer par une cible technique isolée. [1]

Dans notre étude, l’architecte organise donc une discussion autour de situations concrètes : un paiement accepté immédiatement, un rejet explicite, une réponse tardive et une absence de résultat connu. Chaque acteur doit pouvoir dire ce qu’il attend dans ces situations.

## 4. Rendre les attentes vérifiables

Une expression comme « le système doit être rapide » est insuffisante. Il faut préciser quelle opération est mesurée, dans quelles conditions et avec quel résultat attendu. Les valeurs doivent venir du contexte métier, des engagements et des mesures, non d’un seuil choisi pour donner une impression de précision.

Pour la consultation, le temps de réponse ne suffit pas. Il faut également préciser la fraîcheur de l’information. Une vue qui répond immédiatement mais affiche un état ancien peut conduire l’utilisateur à agir sur une mauvaise compréhension de la situation.

Pour la reprise, la question devient : après l’interruption du processus, quelles opérations peuvent être retrouvées et comment déterminer leur dernier état certain ? Pour une interaction externe, elle devient : si une réponse n’arrive pas, comment distinguer absence d’exécution et résultat encore inconnu ?

Le travail consiste à associer une situation, un comportement attendu et une preuve. La précision augmente à mesure que les hypothèses sont confrontées au réel.

## 5. Comprendre les responsabilités avant de distribuer

Le suivi des paiements fait intervenir au moins trois responsabilités : conduire leur cycle de vie, échanger avec un système d’exécution et présenter l’information aux utilisateurs. Elles peuvent être séparées dans le code sans être immédiatement réparties entre plusieurs applications.

Une première proposition peut prendre la forme suivante :

```text
Canal utilisateur
      ↓
Application
├── Cycle de vie des paiements
├── Adaptation du partenaire d’exécution
└── Consultation des états
```

Ce schéma ne fixe ni le nombre de processus ni les produits utilisés. Il rend visibles les responsabilités à discuter. La distinction entre couches logiques et déploiement physique permet précisément de ne pas confondre organisation du code et répartition sur des serveurs. [2]

L’analyse métier doit aussi préciser ce que chaque état signifie. Le DDD insiste sur la construction du modèle en lien avec la réalité du domaine et les cas d’usage. Un mot partagé ne suffit pas si les acteurs lui donnent des sens différents. [3]

## 6. Comparer des options plausibles

Trois options peuvent être étudiées pour notre exemple.

La première conserve l’application et améliore les traitements de suivi. Elle limite l’étendue du changement, mais peut laisser des dépendances internes difficiles à faire évoluer.

La deuxième conserve un déploiement principal tout en restructurant les responsabilités. Elle cherche à clarifier les modules et à isoler les échanges externes, sans ajouter immédiatement les contraintes de plusieurs services indépendants.

La troisième introduit des services distincts. Elle peut répondre à un besoin d’autonomie ou de dimensionnement différencié, mais ajoute des communications distantes, des défaillances partielles et des questions de cohérence.

Les styles architecturaux se choisissent en fonction de leurs propriétés et de leurs compromis. Une architecture microservices peut apporter de l’autonomie, mais sa distribution introduit une complexité qu’il faut prendre en compte. [4]

| Critère | Améliorer l’existant | Restructurer en modules | Distribuer en services |
|---|---|---|---|
| Étendue du changement | Ciblée | Interne, potentiellement importante | Applicative et opérationnelle |
| Livraison indépendante | Non recherchée | Généralement non | Possible si le découpage le permet |
| Contrats réseau internes | Peu de nouveaux | Peu de nouveaux | À concevoir et exploiter |
| Enjeu principal | Corriger le problème sans refonte | Maîtriser les frontières | Obtenir une autonomie utile |

Ce tableau ne désigne pas un vainqueur. Il organise les questions. Une comparaison sérieuse conserve les mêmes exigences pour toutes les options et vérifie les dépendances réelles.

## 7. Faire apparaître les hypothèses fragiles

Supposons que l’équipe recommande une application modulaire. Le choix dépend de plusieurs hypothèses : une seule équipe réalise l’essentiel du travail ; les modules n’ont pas besoin de cycles de livraison indépendants ; la charge reste compatible avec un dimensionnement commun ; le système externe permet un rapprochement par référence.

La dernière hypothèse est particulièrement importante. Si elle est fausse, une grande partie du traitement des résultats inconnus doit être repensée. L’architecture doit donc la signaler comme un point à vérifier, non comme une garantie acquise.

Une décision devient plus solide lorsqu’elle distingue ce qui est connu, ce qui est supposé et ce qui doit être expérimenté. Une incertitude visible peut être traitée. Une incertitude cachée devient souvent un incident ou une réécriture tardive.

## 8. Préserver le sens des résultats inconnus

Imaginons que l’application envoie une demande au partenaire. Celui-ci la traite, mais la réponse n’arrive pas au demandeur. Le dépassement du délai ne permet pas, à lui seul, de conclure que l’action n’a pas eu lieu.

Dans ce scénario fictif, transformer immédiatement l’absence de réponse en rejet produirait une information trompeuse. Renvoyer automatiquement une nouvelle demande pourrait aussi être dangereux si le contrat ne permet pas de reconnaître la répétition d’une même intention.

L’architecture proposée doit donc expliciter le traitement de l’incertitude : conserver la référence, rechercher le résultat, rapprocher les réponses et, si nécessaire, permettre une intervention contrôlée. Le mécanisme exact dépend des garanties offertes par le partenaire.

Ce cas montre pourquoi un schéma nominal est insuffisant. Les difficultés les plus structurantes apparaissent souvent lorsque tous les composants ne réussissent pas au même moment.

## 9. Formaliser la décision sans figer le système

Une décision courte peut résumer le raisonnement : conserver un déploiement principal, séparer les responsabilités internes et différer l’extraction en services. Les motifs sont l’absence de besoin immédiat de livraison indépendante et la volonté de concentrer l’effort sur le modèle d’état et la reprise.

Il faut également écrire les conséquences : une livraison commune reste nécessaire ; les frontières internes doivent être contrôlées ; l’adaptateur du partenaire doit protéger le modèle local ; les mécanismes de reprise doivent être vérifiés.

Enfin, la décision doit préciser ce qui justifierait son réexamen : plusieurs équipes autonomes, une charge très différente selon les fonctions ou une exigence de livraison indépendante. Documenter cette condition ne fragilise pas le choix. Cela évite de transformer une réponse contextuelle en principe permanent.

## 10. Relier chaque promesse à une preuve

L’affirmation « une demande répétée ne crée pas un second effet » exige un test de répétition, mais aussi une analyse de la concurrence et du redémarrage. L’affirmation « le suivi est fiable » exige une comparaison avec les événements et résultats de référence. L’affirmation « la reprise fonctionne » exige un arrêt pendant les étapes sensibles, puis une reprise observée.

Toutes les preuves ne sont pas interchangeables. Un test unitaire peut vérifier une transition d’état. Il ne prouve pas que deux traitements concurrents seront correctement arbitrés par le stockage. Une démonstration nominale prouve que le chemin nominal fonctionne dans les conditions de la démonstration, pas que tous les incidents sont maîtrisés.

Pour notre étude, les premiers essais porteront sur la réponse perdue, la demande répétée, le redémarrage après envoi et la réception d’un résultat tardif. Les seuils de performance seront fixés et mesurés séparément.

## 11. Exercice : remettre la décision dans le bon ordre

Une équipe annonce : « Nous allons remplacer l’application par six microservices sur une plateforme de conteneurs afin de résoudre les problèmes de suivi. »

Reformuler cette proposition en séparant le besoin, les exigences, les contraintes et les options. Identifier trois informations manquantes et deux expériences à réaliser avant de valider le découpage.

Une réponse argumentée commence par le problème utilisateur : les statuts ne permettent pas de comprendre le résultat. Elle précise ensuite les exigences de fraîcheur, de signification et de reprise. Elle recherche notamment le protocole du partenaire, la cause des incohérences et le besoin réel d’autonomie des équipes.

Les expériences peuvent reproduire une réponse perdue et une demande répétée. Le nombre de services reste une hypothèse à discuter après cette analyse. Une plateforme de déploiement ne remplace ni la définition des états ni le contrat de reprise.

## 12. L’architecture comme chaîne de justification

Le travail présenté dans ce chapitre peut se lire comme une chaîne : objectif, exigences, responsabilités, options, décision et preuves. Chaque maillon permet de comprendre le suivant. Si un produit ou un pattern apparaît sans lien avec un besoin, la justification est incomplète. Si une garantie ne possède aucune preuve, elle reste une intention.

Cette chaîne ne s’exécute pas une seule fois dans un ordre rigide. Les essais révèlent des contraintes ; les discussions métier corrigent les modèles ; les incidents conduisent à revoir les hypothèses. Le rôle de l’architecture est de garder cette évolution intelligible.

Le chapitre suivant distinguera les niveaux et les vues qui permettent de représenter ces décisions sans demander à un seul diagramme de raconter tout le système.

## Références

[1] The Open Group, *The TOGAF Standard — Architecture Development Method*, objectifs, contraintes et risques de transformation : https://pubs.opengroup.org/togaf-standard/adm/

[2] Microsoft Learn, *Common web application architectures*, séparation logique et physique : https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures

[3] Microsoft Learn, *Designing a DDD-oriented microservice*, modèle de domaine et responsabilités des couches : https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice

[4] Microsoft Learn, *Architecture styles*, critères et compromis des styles : https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/

[Sommaire](../SOMMAIRE.md)
