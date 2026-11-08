# Chapitre 12 — Gouverner les décisions, les exceptions et la dette

## 1. Donner une portée réelle aux choix d’architecture

Une équipe peut posséder des modèles précis, des exigences mesurables et une cible cohérente sans savoir comment arbitrer un désaccord. Une autre peut obtenir de nombreuses approbations sans que personne ne puisse expliquer les conséquences des choix retenus.

La gouvernance doit résoudre ce décalage. Elle organise les décisions, les responsabilités et le suivi nécessaires pour que les architectures restent cohérentes avec les objectifs. Elle ne se réduit ni à un comité ni à une collection de signatures.

Dans notre organisation fictive, le suivi des paiements dépend d’un partenaire, de plusieurs équipes et de mécanismes de reprise sensibles. Certains choix restent locaux ; d’autres modifient un contrat ou exposent l’organisation à un risque. Les traiter tous avec la même procédure serait aussi peu pertinent que ne rien coordonner.

La question directrice de ce chapitre est donc : qui peut décider de quoi, sur quelle base, et comment saura-t-on si les conditions du choix restent valables ?

## 2. Distinguer les objets de décision

Une décision peut porter sur une règle métier, une conception technique, un investissement ou une acceptation de risque. Les sujets se croisent, mais les mandats ne sont pas nécessairement les mêmes.

Dans notre cas, définir le sens d’un rejet ne relève pas uniquement de la préférence d’un développeur. Choisir l’organisation interne d’un module ne nécessite pas automatiquement une décision d’entreprise. Accepter une reprise sans garantie suffisante sur l’effet distant ne doit pas être dissimulé dans un choix d’interface.

| Objet | Exemple fictif | Point d’attention |
|---|---|---|
| Règle métier | Définir ce qui établit une confirmation | Autorité sur le sens et les effets |
| Conception | Choisir une frontière de module | Conséquences sur les dépendances |
| Contrat partagé | Faire évoluer un événement | Consommateurs et compatibilité |
| Investissement | Financer la capacité de reprise | Valeur, coût complet et calendrier |
| Risque | Accepter une limitation temporaire | Mandat et conditions de réexamen |

Cette répartition est une proposition de raisonnement. Une organisation réelle doit préciser ses propres délégations et obligations.

L’ADM de TOGAF distingue lui-même la gouvernance de mise en œuvre et la gestion du changement d’architecture, en conservant une gestion des exigences à travers les phases. Le travail de décision ne s’arrête donc pas à l’approbation d’une cible. [1]

## 3. Proportionner la gouvernance à l’impact

Une décision de faible portée et facile à corriger peut rester proche de l’équipe. Une décision qui affecte plusieurs consommateurs, des données durables ou une action difficilement réversible appelle une analyse plus large.

Pour notre exemple, renommer un élément interne sans modifier son contrat n’a pas la même portée que changer la signification de `ExecutionConfirmee`. La deuxième modification peut affecter la comptabilité, les notifications et les usages d’assistance.

La taille du document n’est pas une mesure fiable de cette portée. Une phrase autorisant une resoumission automatique peut avoir davantage de conséquences qu’un long choix de mise en page.

Nous proposons d’examiner quatre dimensions : périmètre touché, gravité d’un échec, coût du retour en arrière et incertitude. Elles aident à choisir les personnes à consulter et les preuves nécessaires, sans imposer un score universel.

La gouvernance devient utile lorsqu’elle accélère la bonne décision, pas lorsqu’elle multiplie les étapes sans rapport avec le risque.

## 4. Transformer les principes en critères utilisables

Un principe d’architecture donne une orientation durable. Pour être utile, il doit expliquer son motif et ses conséquences. « Favoriser l’interopérabilité » reste trop vague si toutes les options peuvent s’en réclamer.

Dans notre étude, un principe pourrait être : toute donnée métier partagée doit avoir une autorité identifiable. Sa conséquence est de demander qui peut établir l’état, qui en conserve une copie et comment les divergences sont résolues.

Un autre principe pourrait demander de justifier la distribution par un besoin démontré. Il ne signifie pas « interdire les microservices ». Il impose d’expliquer l’autonomie recherchée et les coûts de communication et d’exploitation acceptés.

Les principes ne doivent pas être confondus avec des obligations externes ni avec un catalogue de produits autorisés. Une préférence interne peut admettre une exception ; une obligation applicable demande une analyse distincte de ce qui est permis et de l’autorité compétente.

Le principe doit pouvoir être réexaminé si ses hypothèses cessent d’être valables. Sa stabilité n’exige pas son immutabilité.

## 5. Préserver la raison des décisions

Connaître une décision sans sa motivation expose à deux réactions : la conserver aveuglément ou la supprimer sans comprendre ce qu’elle protège. Michael Nygard décrit précisément ce problème dans son texte sur les Architecture Decision Records, ou ADR. [2]

Une fiche courte doit conserver les forces en présence, le choix et ses conséquences. Elle porte une décision significative, pas l’ensemble de l’architecture.

Les décisions pertinentes concernent notamment la structure, les qualités, les dépendances, les interfaces ou les techniques de construction. Leur importance vient de leurs effets, non du rang hiérarchique de la personne qui les propose. [2]

Dans notre cas, il est utile de savoir pourquoi le suivi reste dans une application modulaire. Si l’absence de besoin de livraison indépendante motivait le choix, l’apparition d’équipes réellement autonomes peut justifier son réexamen.

Le document doit permettre ce raisonnement futur, plutôt que présenter le choix comme une vérité hors contexte.

## 6. Exemple de décision argumentée

Considérons la décision fictive suivante, présentée dans le corps du livre à des fins pédagogiques.

### Conserver un déploiement principal pour la première transformation

Le besoin immédiat est de clarifier les états, de conserver les références et de fiabiliser le rapprochement. Une équipe porte le périmètre initial. Le partenaire reste inchangé. Aucun besoin de livraison indépendante n’a été établi pour les modules internes.

Deux options sont examinées : restructurer l’application autour de responsabilités explicites ou répartir immédiatement ces responsabilités dans des services indépendants.

La première option est retenue pour cette étape. Elle limite les changements opérationnels simultanés et permet de concentrer la qualification sur les règles et les reprises.

Ce choix conserve une livraison commune. Il exige des frontières internes contrôlées et ne garantit pas un dimensionnement indépendant. Il n’interdit pas une extraction future ; celle-ci sera réexaminée si des besoins d’autonomie ou de charge différenciée sont établis.

Les preuves attendues portent sur les transitions, les accès concurrents, les doublons et les scénarios de réponse perdue. L’acceptation du choix ne signifie pas que ces preuves ont déjà été produites.

Cette présentation applique l’idée d’une décision courte avec contexte et conséquences. Elle ajoute des éléments de vérification adaptés à notre cas, sans les présenter comme un modèle officiel imposé par Nygard.

## 7. Séparer décision acceptée et garantie démontrée

Une décision peut être acceptée sous certaines hypothèses avant que tous les essais soient terminés. Il faut alors distinguer l’accord sur l’orientation et l’autorisation d’exposer un usage réel.

Dans notre exemple, l’équipe peut accepter le principe d’une interrogation de statut par référence. Elle doit encore vérifier les garanties du partenaire. L’ouverture d’une reprise automatique ne doit pas découler de la seule acceptation du principe.

Cette séparation évite une confusion fréquente : une revue a approuvé le schéma, donc le système serait déjà fiable. La revue établit au mieux une décision et les conditions associées ; les mécanismes réalisés demandent leurs propres preuves.

Les conditions doivent être suffisamment précises pour être closes. « Tester la reprise » n’est pas aussi clair que « vérifier la recherche d’un résultat après réception distante et perte de la réponse, puis après redémarrage local ».

Une condition encore ouverte doit rester visible dans le raisonnement de mise en service.

## 8. Organiser une revue qui examine les risques

Une revue utile part de la décision recherchée et du périmètre. Elle examine les contraintes, les alternatives, les hypothèses et les preuves disponibles.

Pour le suivi des paiements, nous proposons de parcourir un scénario nominal, une réponse perdue, une demande répétée et une dépendance indisponible. Les questions portent sur les règles, l’autorité des données, les accès et la reprise.

| Question | Ce qu’elle cherche à établir |
|---|---|
| Quel objectif justifie le choix ? | Lien avec la valeur attendue |
| Quelles options ont été examinées ? | Caractère argumenté de l’arbitrage |
| Quelle hypothèse peut invalider la solution ? | Incertitude structurante |
| Qui assume les conséquences ? | Autorité et responsabilité |
| Quelle preuve manque encore ? | Conditions de réalisation ou d’ouverture |
| Quand réexaminer le choix ? | Déclencheurs de changement |

La revue peut conclure qu’un essai ciblé est nécessaire avant de décider. Elle ne doit pas transformer un manque de connaissance en certitude pour respecter un calendrier.

Les participants contribuent selon leurs compétences et leurs mandats. La présence de toutes les personnes n’implique pas que toute décision exige l’unanimité.

## 9. Traiter les exceptions sans créer un droit permanent

Une exception autorise, dans un périmètre et des conditions précises, un écart à une règle interne ou à une orientation. Elle ne doit pas devenir une manière de contourner silencieusement une exigence indispensable.

Dans notre cas, l’organisation pourrait conserver temporairement un ancien mécanisme de consultation, tout en limitant ses usages et en organisant son retrait. Il faut préciser la raison, les risques, le responsable et les conditions de fin.

La convention proposée pour notre étude comprend la règle concernée, l’écart demandé, le périmètre, les alternatives, les mesures compensatoires, l’autorité d’acceptation et le déclencheur de réexamen.

Une échéance seule ne réduit pas le risque. Elle doit être accompagnée d’un travail réalisable, de ressources et d’un traitement si les conditions de sortie ne sont pas remplies.

Il faut enfin distinguer ce qui relève d’un standard interne et ce qui relève d’une obligation externe. Le mandat d’un comité d’architecture ne lui permet pas automatiquement d’écarter une obligation applicable. L’analyse doit être conduite avec les fonctions compétentes.

## 10. Réexaminer plutôt que renouveler par habitude

Une exception prolongée plusieurs fois peut révéler une cible irréaliste, une priorité insuffisante ou une règle mal adaptée. Le renouvellement ne devrait pas être un simple changement de date.

Pour notre ancien outil de suivi, les questions deviennent : quels usages subsistent, pourquoi ne sont-ils pas couverts, quels contrôles restent effectifs et la cible demeure-t-elle pertinente ?

La réponse peut être de financer la sortie, de modifier la cible ou de reconnaître une solution durable différente. Ce dernier choix exige une nouvelle décision, pas la conservation d’une fiction de transition.

Le réexamen doit intégrer les observations. Si les incidents augmentent ou si le partenaire change son contrat, les conditions initiales d’acceptation peuvent ne plus tenir.

Une gouvernance cohérente traite ces changements sans rechercher un responsable à blâmer pour chaque hypothèse corrigée. Elle exige en revanche de ne pas laisser une information nouvelle sans conséquence sur les décisions qu’elle affecte.

## 11. Comprendre la métaphore de la dette technique

La dette technique décrit, par analogie, des choix ou défauts de conception qui créent un coût futur. L’effort de correction peut être rapproché d’un principal ; la difficulté supplémentaire à modifier ou exploiter le système peut être rapprochée d’intérêts.

Martin Fowler souligne que la métaphore sert surtout à raisonner et à communiquer sur les conséquences des défauts de conception. Il distingue les dettes prudentes ou imprudentes, puis délibérées ou involontaires. [3]

Cette grille aide à reconnaître qu’une dette peut être découverte après un apprentissage, même dans une équipe compétente. Elle ne doit pas devenir un moyen de qualifier moralement les personnes.

Dans notre cas, une interface initialement suffisante peut révéler un couplage coûteux lorsqu’un nouveau parcours apparaît. La découverte doit conduire à examiner les effets et les options de correction, pas seulement à rechercher pourquoi le modèle parfait n’avait pas été prévu dès le départ.

## 12. Quatre lectures de la dette

La distinction de Fowler peut être appliquée à nos exemples sans prétendre mesurer automatiquement leur gravité. [3]

| Lecture | Exemple pédagogique |
|---|---|
| Prudente et délibérée | Conserver temporairement une duplication limitée pour livrer une valeur identifiée, avec conséquences explicites |
| Imprudente et délibérée | Ouvrir un accès direct aux données sans examiner les effets sur les règles et les droits |
| Prudente et involontaire | Découvrir qu’un modèle bien conçu doit évoluer après de nouveaux scénarios |
| Imprudente et involontaire | Introduire un couplage important sans comprendre ses conséquences |

Ces exemples ne sont pas des verdicts universels. Le contexte et les conséquences réelles doivent être établis.

Une simplification volontaire n’est pas automatiquement une dette. Choisir une solution simple adaptée au besoin peut être une bonne conception. À l’inverse, qualifier un risque grave de dette ne le rend pas acceptable.

Le coût de correction doit être comparé au coût de maintien de la situation. Tout écart esthétique ne justifie pas une refonte immédiate.

## 13. Distinguer dette, risque, défaut et obsolescence

Une dette peut accroître le coût du changement. Un défaut représente un comportement incorrect par rapport au besoin. Un risque concerne une incertitude et ses conséquences. Une obsolescence concerne l’évolution d’un support, d’une compétence ou d’une technologie. Ces situations peuvent se recouper sans se confondre.

Dans notre cas, afficher un succès sans confirmation peut être un défaut métier à traiter. Une interface couplée peut créer de la dette. Une dépendance bientôt non maintenue peut introduire une contrainte d’obsolescence et un risque.

Les regrouper dans un unique backlog de dette ferait perdre les priorités et les responsabilités adaptées. Une vulnérabilité importante ne devient pas un simple choix de confort parce qu’elle figure parmi les travaux techniques.

Il faut décrire le mécanisme de coût ou de dommage : changements systématiquement multiéquipes, interventions manuelles, erreurs répétées, difficulté de reprise ou impossibilité de faire évoluer le contrat.

Une dette expliquée en termes d’effets est plus facile à arbitrer qu’une demande générale de nettoyage.

## 14. Prioriser les corrections selon leurs conséquences

Pour notre étude, nous proposons d’examiner la fréquence des situations touchées, l’effort supplémentaire, le risque, les dépendances et les changements prévus. Les estimations doivent rester présentées comme des estimations.

Une zone rarement modifiée peut supporter un coût faible malgré une conception peu élégante. Une petite interface très utilisée peut bloquer plusieurs équipes. Fowler rappelle également qu’un remboursement n’est pas toujours justifié lorsque le coût récurrent reste faible. [3]

| Situation fictive | Question d’arbitrage |
|---|---|
| Duplication dans un module stable | Quel coût réel produit-elle ? |
| Contrat partagé modifié fréquemment | Quels délais et erreurs provoque le couplage ? |
| Reprise manuelle répétée | Quel effort et quelle exposition produit-elle ? |
| Dépendance non maintenue | Quelles obligations, capacités et alternatives sont concernées ? |

Il faut aussi examiner les effets de la correction. Une extraction de service peut réduire un couplage interne tout en ajoutant des communications et des reprises distribuées.

L’objectif est de réduire une conséquence démontrée, pas de rembourser un chiffre produit automatiquement par un outil sans analyse du contexte.

## 15. Faire entrer les décisions dans la réalisation

Une décision n’a de portée que si les équipes peuvent l’appliquer et vérifier ses conséquences. Les contrats, les tests et les contraintes de dépendance peuvent traduire certaines règles dans la réalisation.

Pour notre application, interdire l’accès direct à un état ne suffit pas dans un document. Les API et les droits doivent empêcher les chemins de contournement. Une frontière de module peut être soutenue par des tests de dépendances. Une règle de compatibilité doit être examinée dans les tests de contrat.

Toutes les décisions ne sont pas automatisables. L’acceptation d’un compromis métier ou la pertinence d’un modèle exige des acteurs et des discussions. L’automatisation est une preuve partielle, pas un remplacement universel de la gouvernance.

Il faut également relier les travaux de correction à des résultats observables. Une revue de code terminée ne démontre pas à elle seule une réduction des opérations incertaines ou du temps de reprise.

## 16. Utiliser les observations pour guider les changements

Les incidents, les mesures et les retours d’usage peuvent révéler qu’une hypothèse n’est plus valable. Une décision doit pouvoir être remplacée sans effacer la compréhension de son origine.

Nygard recommande de conserver les décisions anciennes en indiquant qu’elles ont été remplacées et en les reliant à leur successeur. Le contexte antérieur reste utile pour comprendre l’évolution. [2]

Dans notre cas, l’extraction d’un module peut devenir justifiée après l’apparition d’équipes autonomes. Il faut alors expliquer ce qui a changé, les alternatives et les conséquences supplémentaires. Dire que la décision précédente était simplement « mauvaise » ferait perdre le raisonnement contextuel.

Les objectifs de service peuvent aussi orienter les priorités. Le Site Reliability Workbook présente le budget d’erreur comme un moyen d’appuyer les arbitrages sur la fiabilité, à condition qu’une politique soit convenue avec les acteurs. [4]

Un dépassement doit conduire à comprendre les causes. Il ne justifie ni une refonte automatique ni une modification silencieuse de l’indicateur.

## 17. Préparer les décisions d’urgence

L’urgence réduit le temps disponible mais ne supprime pas les effets d’une intervention. Il faut savoir qui coordonne, qui est habilité à agir et quels éléments doivent être conservés pour comprendre l’action.

Dans notre cas, un résultat inconnu ne doit pas être remplacé manuellement par une confirmation pour faire disparaître une alerte. Une intervention doit s’appuyer sur un fait ou un protocole reconnu, selon les mandats établis.

Après le rétablissement, la décision temporaire doit être revue. Certaines modifications doivent être retirées ; d’autres peuvent devenir une solution durable après analyse. La continuité entre incident et conception évite que les exceptions opérationnelles deviennent une architecture cachée.

Cette proposition ne définit pas un processus universel de crise. Les exigences de sécurité, d’exploitation et les obligations propres à l’organisation doivent être intégrées à la procédure réelle.

## 18. Mesurer la gouvernance sans compter les signatures

Un grand nombre de revues ne prouve pas la cohérence des choix. Un délai court ne prouve pas non plus la qualité de la décision.

Pour notre étude, des observations utiles peuvent concerner les décisions bloquées, les conditions d’acceptation non closes, les exceptions prolongées, les corrections non financées et les incidents associés à des hypothèses déjà connues.

Ces observations doivent rester reliées au contexte. Une exception longue peut venir d’un contrat externe difficile à modifier. Une décision rapide peut être appropriée si le périmètre est réduit et bien connu.

La gouvernance doit également pouvoir se corriger. Si une règle crée un détour coûteux sans protéger une propriété utile, il faut la réexaminer. Si toutes les équipes demandent la même exception, le problème peut se trouver dans le principe ou la capacité commune proposée.

La finalité est de rendre les décisions compréhensibles et leurs conséquences maîtrisables, pas d’augmenter la production administrative.

## 19. Exercice : le temporaire qui ne se termine pas

Une ancienne fonction de suivi devait être retirée après l’ouverture du nouveau parcours. Elle reste utilisée pour quelques recherches historiques. Une exception a été prolongée deux fois. Un accès direct permet encore à certains agents de modifier le statut.

Identifier les décisions à séparer, les preuves manquantes et les responsabilités à clarifier. Proposer une sortie qui ne repose pas sur une nouvelle date seule.

Une réponse argumentée distingue la consultation historique, la mutation de l’état et le retrait technique. Elle vérifie les usages, les obligations de conservation et les garanties d’accès. Elle examine séparément le risque de modification directe et le coût de maintien de l’ancien outil.

La sortie peut demander une consultation en lecture seule, une migration d’historique ou une autre solution justifiée. Elle doit inclure la couverture des recherches nécessaires, le transfert des responsabilités et la vérification des chemins de modification.

Le renouvellement éventuel ne peut pas remplacer ces décisions. Si la cible doit changer, il faut l’exprimer explicitement.

## 20. Conclusion du tome I : relier les choix du métier à la production

Ce premier tome a établi un raisonnement commun. Partir d’un objectif ne suffit pas : il faut comprendre les acteurs, formuler les exigences, reconnaître les capacités et décrire l’existant comme la cible. Les méthodes et langages aident à structurer ce travail, à condition de ne pas les confondre avec les décisions elles-mêmes.

TOGAF organise une transformation architecturale. O-AA éclaire l’architecture dans une évolution agile et numérique. Zachman structure les descriptions. IT4IT relie la gestion des produits numériques. DDD approfondit les modèles métier. SABSA relie la protection aux objectifs et aux risques. Les langages de modélisation rendent certains aspects plus précis.

La gouvernance maintient leur articulation dans la durée. Elle attribue les décisions, préserve leurs raisons, traite les écarts et utilise les observations pour réexaminer les choix. Elle reste utile lorsqu’elle permet d’agir avec une compréhension plus claire des conséquences.

Le tome II passera de ces fondements à la conception des solutions et des applications. Il commencera par délimiter une solution : ses acteurs, son périmètre, ses responsabilités et les contrats qui la relient au reste du système d’information.

## Références

[1] The Open Group, *The TOGAF Standard — Architecture Development Method*, gouvernance de mise en œuvre, gestion du changement et exigences : https://pubs.opengroup.org/togaf-standard/adm/

[2] Michael Nygard, *Documenting Architecture Decisions*, Cognitect, 2011, motivation des décisions, format ADR et conservation des décisions remplacées : https://www.cognitect.com/blog/2011/11/15/documenting-architecture-decisions

[3] Martin Fowler, *Technical Debt Quadrant*, 2009, métaphore de dette, distinctions prudent-imprudent et délibéré-involontaire : https://martinfowler.com/bliki/TechnicalDebtQuadrant.html

[4] Google, *The Site Reliability Workbook — Implementing SLOs*, objectifs, budget d’erreur et politique de décision : https://sre.google/workbook/implementing-slos/

[Chapitre précédent](chapitre-11.md) · [Sommaire](../SOMMAIRE.md)
