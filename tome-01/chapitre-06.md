# Chapitre 6 — TOGAF et la conduite de la transformation

## 1. Organiser le raisonnement plutôt qu’accumuler les documents

Les chapitres précédents ont relié objectifs, capacités, exigences, architectures et trajectoires. Il reste à organiser ce travail lorsqu’il traverse plusieurs projets, plusieurs équipes et plusieurs domaines du système d’information.

TOGAF fournit un cadre pour développer et gouverner l’architecture. Son Architecture Development Method, ou ADM, est défini comme une démarche itérative en plusieurs phases destinée à façonner et à gouverner la transformation de l’entreprise. [1]

Le cadre n’est pas une architecture prête à installer. Il ne prescrit ni le nombre de microservices ni un fournisseur cloud. Il aide à organiser les questions, les contributions et les décisions. Une organisation peut produire des documents portant tous les intitulés attendus sans avoir clarifié une seule responsabilité critique. À l’inverse, une application proportionnée de la méthode peut rester lisible et orientée vers des arbitrages concrets.

Dans notre cas fictif, nous utiliserons l’ADM pour comprendre comment améliorer le suivi des paiements sans perdre le lien entre besoin métier, données, applications, plateforme et réalisation.

## 2. Les quatre domaines et les préoccupations transverses

TOGAF distingue quatre domaines principaux : métier, données, applications et technologie. D’autres préoccupations, dont la sécurité et la gouvernance, peuvent les traverser. [1]

| Domaine | Question appliquée au cas fictif |
|---|---|
| Métier | Comment prendre en charge, expliquer et résoudre une demande ? |
| Données | Quels états, références et historiques faut-il maîtriser ? |
| Applications | Quels systèmes portent les responsabilités et leurs interactions ? |
| Technologie | Quelles capacités d’exécution et d’exploitation sont nécessaires ? |

Ces domaines ne forment pas des silos indépendants. Une règle métier de rapprochement peut demander une donnée durable, une responsabilité applicative et une capacité d’exécution après incident.

Le domaine applicatif de TOGAF décrit notamment la structure et les interactions des applications qui soutiennent les capacités métier et gèrent les données. Il ne doit pas être réduit à l’organisation des classes d’un logiciel. La conception détaillée peut ensuite être approfondie avec d’autres approches. [1]

## 3. Lire le cycle ADM

L’ADM comprend une phase préliminaire, les phases A à H et une gestion des exigences qui accompagne le travail. Les phases B à D développent les architectures métier, des systèmes d’information et technologique. Les phases suivantes traitent les solutions, la migration, la gouvernance de réalisation et l’évolution. [2]

```text
Préparer la pratique d’architecture
                  ↓
A — Vision
                  ↓
B — Métier
                  ↓
C — Données et applications
                  ↓
D — Technologie
                  ↓
E — Opportunités et solutions
                  ↓
F — Planification de la migration
                  ↓
G — Gouvernance de mise en œuvre
                  ↓
H — Gestion du changement d’architecture

Gestion des exigences : à travers les phases
Itérations : entre les travaux et au fil des changements
```

Cette représentation linéaire facilite la lecture mais ne constitue pas une obligation de cascade. Le standard présente l’ADM comme itératif et prévoit son adaptation, la définition du périmètre et l’examen d’alternatives. [1][2]

Une contrainte découverte pendant une expérimentation technique peut conduire à revoir la cible. Une ambiguïté sur les données peut obliger à reprendre une règle métier. L’enjeu est de maîtriser les conséquences de ces retours, non de les interdire.

## 4. La phase préliminaire : rendre la pratique possible

La phase préliminaire établit notamment le périmètre organisationnel, les cadres de gouvernance, l’organisation de l’architecture, les principes et l’adaptation du cadre aux besoins. [2]

La question n’est pas encore « comment concevoir le suivi ? », mais « comment l’organisation va-t-elle conduire ce travail ? ». Qui peut arbitrer une responsabilité partagée ? Quels principes s’appliquent ? Quelles compétences sont disponibles ? Comment une exception est-elle décidée ?

Pour notre organisation fictive, cette préparation peut établir que les définitions d’états sont arbitrées avec le métier, que les responsabilités de données doivent être explicites et que les mécanismes de reprise sont revus avec l’exploitation.

Un principe utile possède des conséquences. « Nous favorisons la simplicité » devient exploitable si l’équipe doit justifier l’introduction d’un composant distribué par un besoin identifié. Il reste insuffisant s’il ne permet jamais de comparer deux options.

Cette préparation doit rester proportionnée. Une pratique déjà établie peut être réutilisée ; il n’est pas nécessaire de recréer une organisation complète pour chaque changement.

## 5. Phase A : donner une vision et un mandat

La phase A identifie les parties prenantes, leurs préoccupations, les objectifs, les contraintes, le périmètre, la valeur attendue et les risques. Elle comprend également le Statement of Architecture Work et son approbation. [2]

Nous traduirons ce dernier comme un cadrage du travail d’architecture : ce qui doit être étudié, dans quel périmètre et avec quelles attentes. Il ne faut pas le confondre avec le contrat technique de chaque application.

Dans notre cas, la vision pourrait être formulée ainsi : fournir une compréhension cohérente du résultat connu d’un paiement et organiser la résolution des situations incertaines, dans les canaux clients et l’assistance.

Cette vision ne promet pas encore une réponse définitive instantanée. Elle respecte les limites connues du partenaire et précise la valeur recherchée. Les indicateurs de résultat, tels que le délai de clarification, devront être définis et mesurés dans le contexte réel.

Le mandat doit également expliciter les exclusions. La première transformation peut ne pas remplacer le moteur d’exécution externe. Cette frontière évite de supposer une maîtrise que le projet ne possède pas.

## 6. Phase B : concevoir le fonctionnement métier

La phase B développe les descriptions métier existante et cible, analyse leurs écarts, examine les impacts et organise la revue avec les parties prenantes. [2]

Dans notre exemple, elle clarifie le cycle de prise en charge, les rôles et les activités de résolution. Qui explique un résultat au client ? Qui recherche une confirmation ? Quelle situation nécessite une intervention habilitée ?

L’architecture métier doit inclure les exceptions, pas seulement le parcours idéal. Un processus qui se termine par « traitement manuel » sans responsabilité ni information disponible reste incomplet.

Le résultat attendu n’est pas seulement un diagramme d’activités. Il faut comprendre comment la capacité de suivi fonctionne, quelles informations elle utilise et où ses limites actuelles empêchent la valeur recherchée.

Les décisions du domaine métier peuvent alors orienter les autres domaines. L’obligation de distinguer attente et rejet, par exemple, impose de préserver cette différence dans les données et les contrats applicatifs.

## 7. Phase C : relier données et applications

La phase C couvre les architectures des données et des applications. Pour chacune, le standard décrit le développement de l’existant et de la cible, l’analyse des écarts et la prise en compte des impacts sur l’ensemble architectural. [2]

Côté données, notre étude doit préciser les concepts, leur autorité et leur circulation. Quel élément prouve une confirmation ? Quelle référence permet de rechercher une demande ? Quelles copies servent à la consultation ?

Côté applications, elle attribue les responsabilités : gérer le paiement, adapter le partenaire, rapprocher le résultat et exposer l’information. Ces responsabilités ne conduisent pas automatiquement à quatre services déployés séparément.

Les deux travaux se répondent. Une copie destinée à la consultation ne doit pas être présentée comme l’autorité qui décide de l’état. Une responsabilité de rapprochement ne peut pas fonctionner si les références indispensables ne sont pas conservées.

La phase C ne remplace pas la conception détaillée des agrégats, des packages et des transactions. Elle établit les relations structurantes qui rendent cette conception cohérente avec le périmètre de transformation.

## 8. Phase D : définir les capacités technologiques

La phase D développe l’architecture technologique existante et cible, leurs écarts et les impacts associés. [2]

Dans notre cas, la technologie doit soutenir les exigences établies : persistance, communications, observation, contrôle des accès, capacité et reprise. Le choix d’un runtime ou d’une plateforme doit être relié à ces besoins.

Le raisonnement commence par les services techniques attendus. Il se poursuit par l’examen des solutions disponibles, de leurs contraintes, de leurs compétences d’exploitation et de leur coût.

Une plateforme qui redémarre les instances ne résout pas à elle seule l’incertitude d’une demande externe. Une réplication de données ne définit pas la politique de rapprochement. Le domaine technologique doit donc expliquer sa contribution sans absorber les responsabilités applicatives et métier.

Cette phase peut aussi révéler une incompatibilité. Si la cible exige une capacité indisponible dans l’environnement prévu, il faut réexaminer l’option ou sa trajectoire plutôt que masquer la difficulté dans un schéma.

## 9. Phase E : composer les solutions et les transitions

La phase E consolide les écarts des domaines, réconcilie les besoins d’interopérabilité, vérifie les dépendances et identifie les principaux lots de travail et architectures de transition. Elle contribue à la stratégie et à la feuille de route de mise en œuvre. [2]

Le passage est important : une liste d’écarts ne constitue pas encore une solution réalisable. Il faut comprendre quelles actions se combinent et quelles conditions doivent être réunies.

Pour notre cas, améliorer la capture des références, définir le modèle d’états et exposer une consultation cohérente peuvent constituer des ensembles de changement liés. Le rapprochement automatique peut dépendre d’une évolution du contrat partenaire.

Une transition décrit un état exploitable du système. Si l’ancien et le nouveau suivi coexistent, il faut définir les autorités, les flux et les modalités de reprise. Une transition incohérente ne devient pas acceptable simplement parce qu’elle est provisoire.

La composition doit examiner les options de réutilisation et d’adaptation, pas supposer que tout doit être reconstruit.

## 10. Phase F : ordonner et financer la migration

La phase F approfondit la valeur des lots, les ressources, les échéances, les coûts, les bénéfices et les risques pour finaliser le plan de mise en œuvre et de migration. [2]

La différence avec E est une dominante de travail, non une séparation hermétique. E compose les solutions et les transitions ; F rend leur réalisation priorisée et planifiable avec les cadres de gestion concernés.

Dans notre exemple, automatiser une résolution sans disposer de références fiables serait un ordre fragile. Retirer l’ancien outil avant de couvrir les recherches historiques serait une prise de risque supplémentaire.

Le plan doit tenir compte des compétences, des contrats externes, des accès et de la coexistence. Le coût de la cible n’est pas le coût complet de la transformation : l’ancien et le nouveau peuvent devoir être financés ensemble pendant un temps.

Les bénéfices doivent également être attribués au bon changement. Un nouvel outil ne réduit le travail manuel que si le processus et les responsabilités évoluent effectivement.

## 11. Phase G : accompagner la réalisation

La phase G traite notamment l’alignement du déploiement avec les priorités, les ressources et compétences, les revues de conformité architecturale, les opérations et la revue après mise en œuvre. [2]

Gouverner la réalisation ne signifie pas remplacer l’équipe de développement ni inspecter chaque ligne de code. Il s’agit de vérifier que les décisions structurantes se traduisent dans les mécanismes réalisés, et de traiter les écarts utiles ou inévitables.

Pour notre solution, la revue doit demander où la référence est persistée, comment une demande répétée est traitée et comment les opérations incertaines sont reprises. Le nom d’un pattern ne constitue pas la preuve attendue.

Une découverte peut justifier une adaptation. Si le partenaire ne respecte pas une hypothèse, il faut réévaluer les impacts, faire arbitrer la modification et actualiser la compréhension de la cible. La conformité ne doit pas consister à maintenir une conception devenue fausse.

La préparation de l’exploitation et la revue après réalisation relient enfin l’architecture au service réellement fourni.

## 12. Phase H : décider comment faire évoluer l’architecture

La phase H porte sur la gestion du changement d’architecture. L’ADM inscrit ainsi la conception dans une évolution continue plutôt que dans une production ponctuelle de cible. [2]

Un incident, une nouvelle règle métier, une dépendance remplacée ou une modification de stratégie peut remettre en cause des hypothèses. Il faut alors examiner l’ampleur du changement et ses impacts.

Dans notre exemple, un nouveau protocole partenaire peut supprimer certaines incertitudes et en créer d’autres. Une augmentation de charge peut rendre une responsabilité candidate à un déploiement distinct. Une fusion de parcours peut modifier les besoins de consultation.

Tous les changements n’exigent pas une étude complète de toute l’entreprise. Il faut adapter le périmètre du travail à l’impact. L’architecture demeure une aide à la décision si elle permet de reconnaître ce qui reste valable et ce qui doit être revu.

## 13. La gestion des exigences n’attend pas la fin

La gestion des exigences accompagne les phases. Le standard lui consacre une composante propre de l’ADM. [2]

Une exigence nouvelle doit pouvoir être reliée aux décisions concernées. Si une règle impose de limiter les données exposées au partenaire, ses conséquences peuvent toucher le processus, le modèle de données, le contrat d’intégration et les traces d’exploitation.

Dans l’autre sens, une impossibilité technique peut conduire à discuter la formulation d’une attente. Cela ne donne pas à l’équipe le droit de la supprimer silencieusement. Le changement doit être compris et accepté par l’autorité concernée.

La traçabilité utile permet de répondre à trois questions : pourquoi cet élément existe-t-il, quelle exigence réalise-t-il et que faut-il réexaminer si cette exigence change ? Elle ne se mesure pas au volume de tableaux.

## 14. Comprendre ABB et SBB

Un Architecture Building Block, ou ABB, décrit une brique au niveau logique ou indépendant du fournisseur. Un Solution Building Block, ou SBB, désigne un composant de réalisation qui concrétise tout ou partie de ces briques logiques. TOGAF précise que les SBB peuvent relever du métier, des applications ou de la technologie. [1]

Dans notre cas, une capacité de consultation avec autorisation et fraîcheur explicite peut être décrite logiquement avant de choisir les composants qui la réalisent. La solution retenue peut réunir une application, un stockage et des mécanismes d’accès configurés.

| Niveau | Exemple pédagogique |
|---|---|
| ABB | Service de consultation présentant le dernier état connu avec ses conditions d’accès |
| SBB | Réalisation concrète de ce service avec composants et configuration identifiés |

Il ne faut pas conclure qu’un ABB correspond toujours à un microservice, ni qu’un SBB est nécessairement un produit acheté. La distinction aide surtout à séparer besoin architectural et réalisation.

## 15. Livrable, artefact et décision

TOGAF distingue le livrable convenu et formellement examiné, l’artefact qui décrit un aspect de l’architecture et la brique architecturale ou de solution. [1]

Un dossier peut contenir plusieurs vues. Une vue peut mobiliser des diagrammes et des matrices. Aucun de ces objets ne garantit que la décision est bonne : il faut comprendre le contexte, les alternatives et les conséquences.

Le dépôt de connaissances d’architecture conserve les éléments réutilisables et les descriptions utiles à l’organisation. L’Enterprise Continuum fournit une logique de classification allant du générique au spécifique. Ces notions ne prescrivent pas un outil unique. [1]

Dans la pratique proposée ici, on conserve ce qui permet d’expliquer ou de réexaminer les décisions : une frontière, un contrat, une hypothèse vérifiée, une transition ou une limitation. Une collection de documents devenus inexacts ne forme pas une mémoire fiable.

## 16. Adapter sans vider la méthode de son sens

Adapter l’ADM signifie choisir le périmètre, la profondeur, les techniques et l’organisation du travail selon le contexte. Le standard prévoit explicitement cette adaptation. Les Series Guides abordent aussi l’agilité d’entreprise et l’application de l’ADM avec des sprints. [2][3]

Cela ne signifie pas supprimer les préoccupations difficiles. Une équipe peut regrouper certaines activités et limiter les livrables, mais elle doit toujours comprendre les exigences, les impacts et les risques pertinents.

Pour un petit changement de consultation, l’étude peut rester ciblée. Pour une transformation de plusieurs domaines, des travaux plus étendus seront nécessaires. La quantité de documents ne doit pas devenir le critère de sérieux.

La collaboration avec la réalisation doit commencer tôt. Un prototype peut vérifier une hypothèse de données ou d’intégration avant de figer les choix. L’itération sert à apprendre, pas à reporter indéfiniment les décisions.

## 17. Articuler TOGAF avec DDD et les autres approches

TOGAF organise le travail architectural de transformation. DDD approfondit la compréhension et la conception des modèles métier. ArchiMate fournit un langage de représentation. Un cadre de réalisation agile organise d’autres aspects du travail collectif. Il faut examiner leur complémentarité plutôt que chercher un remplacement universel.

Dans notre cas, l’étude métier peut faire apparaître des responsabilités de suivi et de résolution. Les ateliers DDD peuvent ensuite révéler des frontières de modèle plus fines ou corriger les termes utilisés. Cette découverte peut revenir vers l’architecture d’ensemble.

Il n’existe donc pas de correspondance automatique entre une phase ADM et une technique logicielle. La phase C ne signifie pas « faire des microservices » ; la phase D ne signifie pas « installer Kubernetes » ; la phase G ne signifie pas « attendre la livraison pour découvrir les écarts ».

Le rapprochement doit conserver la question traitée par chaque approche. Le cadre organise le raisonnement, mais les compétences et les preuves restent indispensables.

## 18. Exercice : reconnaître une mauvaise application de l’ADM

Un programme décide de terminer tous les documents des phases B à D avant de consulter les équipes de réalisation. Il sélectionne une plateforme en phase D, reporte les échanges avec le partenaire à la migration et prévoit de vérifier les reprises lors de la mise en production.

Expliquer pourquoi cette organisation fragilise la transformation. Proposer une manière d’apprendre plus tôt sans supprimer la gouvernance.

Une réponse argumentée relève que les hypothèses les plus structurantes restent non vérifiées. Les données et le contrat partenaire peuvent remettre en cause le fonctionnement cible. Les équipes de réalisation et d’exploitation doivent contribuer avant que ces décisions ne soient figées.

Des essais ciblés peuvent vérifier la conservation des références, la recherche d’un résultat et les contraintes de reprise. Les constats alimentent les exigences et les options. Les revues restent nécessaires, mais elles portent alors sur une compréhension enrichie plutôt que sur des documents isolés.

Le problème n’est pas la présence de phases. C’est leur interprétation comme des barrières empêchant l’apprentissage.

## 19. Une méthode au service de résultats explicites

L’intérêt de TOGAF se comprend dans la continuité des décisions : établir un mandat, décrire les architectures pertinentes, comparer les écarts, organiser les transitions, accompagner la réalisation et gérer l’évolution.

L’ADM ne remplace ni la connaissance métier ni l’ingénierie. Il ne garantit pas qu’une architecture est résiliente ou économique. Il fournit une structure pour que les objectifs, les contributions, les arbitrages et les impacts restent reliés.

Dans notre cas, la réussite ne serait pas d’avoir parcouru les lettres A à H. Elle serait de fournir un suivi compréhensible, une résolution maîtrisée des situations incertaines et une trajectoire réellement exploitable. Le chapitre suivant examinera d’autres cadres et référentiels pour préciser leurs apports respectifs sans les confondre avec l’ADM.

## Références

[1] The Open Group, *The TOGAF Standard — Introduction, Definitions*, domaines, ADM, ABB, SBB, livrables et continuum : https://pubs.opengroup.org/togaf-standard/introduction/chap04.html

[2] The Open Group, *The TOGAF Standard — Architecture Development Method*, organisation des phases et gestion des exigences : https://pubs.opengroup.org/togaf-standard/adm/

[3] The Open Group, *TOGAF Series Guides*, guides pratiques, agilité et adaptation : https://www.opengroup.org/togaf/series-guides

[Chapitre précédent](chapitre-05.md) · [Sommaire](../SOMMAIRE.md)
