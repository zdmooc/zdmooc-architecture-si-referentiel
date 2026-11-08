# Chapitre 3 — Les acteurs, les responsabilités et la collaboration

## 1. Une architecture ne se construit pas seul

Les vues du chapitre précédent rendent le système compréhensible. Elles ne disent pas encore qui peut décider, qui doit être consulté et qui assumera les conséquences d’un choix. Une architecture peut être cohérente sur le papier et rester inapplicable si ces questions ne sont pas résolues.

Dans notre organisation fictive, le suivi des paiements mobilise une équipe produit, des experts métier, des développeurs, une équipe plateforme, la sécurité et l’exploitation. Tous peuvent employer le mot « fiabilité » tout en désignant des attentes différentes. Le métier pense à l’exactitude du résultat. La plateforme pense à la disponibilité des ressources. Le développement pense à la correction du code. L’assistance pense à la possibilité d’expliquer une situation au client.

Aucune de ces préoccupations n’est illégitime. Le travail collectif consiste à les relier sans supposer qu’un acteur peut répondre pour tous les autres. L’architecte ne remplace pas les compétences spécialisées : il facilite leur articulation et rend visibles les décisions qui traversent leurs frontières.

## 2. Distinguer personne, rôle et autorité

Une personne peut exercer plusieurs rôles. Dans une petite équipe, le tech lead peut également porter la conception applicative. Dans un grand programme, plusieurs spécialistes peuvent se répartir un périmètre d’architecture. Il serait donc trompeur de déduire l’organisation réelle du seul intitulé des postes.

Nous distinguerons trois notions. La personne est l’individu qui intervient. Le rôle décrit une contribution attendue. L’autorité précise les décisions que cette personne ou cette instance est habilitée à prendre dans le contexte considéré.

Être compétent sur une question ne signifie pas automatiquement avoir le mandat d’en accepter les conséquences. Un architecte peut recommander une dérogation ; il ne peut pas supposer qu’il est autorisé à accepter n’importe quel risque pour l’organisation. Cette autorité doit être définie.

Les textes de gouvernance de The Open Group insistent sur la responsabilité et la nécessité d’identifier les structures autorisées à agir et à décider. Les références historiques employées ici éclairent ces principes ; elles ne définissent pas l’organigramme obligatoire d’une entreprise. [1]

## 3. Les acteurs métier et produit

L’expert métier explique les règles, les exceptions et le sens des informations. Dans notre cas, il doit notamment préciser ce qui permet d’affirmer qu’un paiement est exécuté ou rejeté. Il peut connaître les usages réels que les procédures formelles décrivent mal.

Le responsable produit porte, selon le mandat de l’organisation, la vision du service et l’arbitrage de sa valeur. L’analyste métier ou fonctionnel aide à formaliser les besoins, les processus et les règles. Ces fonctions peuvent se recouvrir ; il faut surtout savoir à qui soumettre une ambiguïté et qui peut trancher son interprétation.

Le client et l’assistance apportent un autre regard. Une expression techniquement correcte peut rester incompréhensible. « Demande acceptée » signifie-t-elle que l’application l’a reçue ou que l’opération est définitivement réalisée ? L’architecture doit permettre un contrat intelligible, pas seulement un état stocké.

Le DDD rappelle l’importance de construire les modèles à partir de la réalité métier et des cas d’usage. Cette collaboration n’est pas un entretien unique au démarrage : elle reste nécessaire lorsque le modèle rencontre de nouvelles situations. [2]

## 4. Les contributions des architectes

Les intitulés et les frontières varient selon les entreprises. Pour raisonner dans ce livre, nous utiliserons une répartition indicative, non une classification normative.

| Contribution | Attention principale | Exemple de question |
|---|---|---|
| Architecture d’entreprise | Cohérence du SI et trajectoire | Quelle capacité et quel patrimoine transformer ? |
| Architecture de domaine | Cohérence d’un périmètre | Quels principes et modèles communs retenir ? |
| Architecture solution | Réponse de bout en bout | Comment répartir les responsabilités entre systèmes ? |
| Architecture applicative | Structure du logiciel | Où placer les règles et contrôler les dépendances ? |
| Architecture données | Sens, autorité et circulation | Qui maîtrise l’état et ses copies ? |
| Architecture technique ou plateforme | Conditions d’exécution | Quelles capacités d’hébergement et de reprise fournir ? |
| Architecture sécurité | Protection selon besoins et risques | Quels contrôles et quelles preuves sont nécessaires ? |

L’architecte solution relie ces perspectives sur un besoin donné. Cela ne lui donne pas la maîtrise absolue du métier, des données ou de l’exploitation. Sa contribution consiste notamment à révéler les incohérences entre les réponses partielles.

Si deux équipes affirment posséder le statut définitif du paiement, il faut clarifier cette autorité. Si le métier exige une reprise mais qu’aucun composant ne la réalise, il faut résoudre ce manque. Si la plateforme garantit le redémarrage d’un processus, il reste à établir ce que l’application doit faire après ce redémarrage.

## 5. Développeurs et tech leads : confronter le modèle au code

Une architecture n’est pas terminée lorsqu’un diagramme est accepté. L’implémentation révèle des contraintes : modèle trop couplé, transactions difficiles, interfaces insuffisantes ou dépendances cachées.

Le tech lead contribue à la cohérence de réalisation et à l’explicitation des conséquences techniques. Les développeurs construisent les mécanismes et leurs tests. Ils ne sont pas de simples exécutants d’un modèle figé. Ils doivent pouvoir proposer une correction lorsque le code ou une expérience montre que l’hypothèse initiale ne tient pas.

Dans notre application, une règle de transition peut être claire dans un test unitaire, mais échouer lorsque deux opérations concurrentes utilisent le même état. Cette découverte doit revenir dans la discussion de conception. Elle peut affecter l’agrégat, le stockage ou le contrat du cas d’usage.

À l’inverse, une facilité d’implémentation ne doit pas modifier silencieusement le sens métier. Remplacer un statut incertain par un rejet parce que cela simplifie un écran change le contrat du service. Le compromis doit être expliqué et arbitré avec les acteurs concernés.

## 6. L’exploitation et la plateforme participent à la conception

Un logiciel qui démarre n’est pas nécessairement exploitable. Il faut savoir reconnaître un incident, le localiser, décider d’une reprise et vérifier son résultat.

L’exploitation apporte les contraintes concrètes : plages d’intervention, accès nécessaires, signaux disponibles, procédures et responsabilités. L’équipe plateforme fournit des capacités communes. Selon l’organisation, elle peut prendre en charge le runtime, le réseau, le stockage ou certains services d’observabilité. Ces frontières doivent être explicites.

Dans notre cas, une instance applicative peut être redémarrée automatiquement alors qu’une demande externe reste sans résultat connu. Le redémarrage règle une situation de processus, pas le sens de l’opération distante. L’application doit conserver les éléments permettant la reprise, et un responsable doit traiter les cas non résolus.

| Situation du cas fictif | Contribution attendue |
|---|---|
| Processus arrêté | Détecter et rétablir l’exécution selon le partage opérationnel |
| Résultat externe inconnu | Retrouver la référence et rapprocher le résultat |
| État affiché en retard | Mesurer le retard et expliquer la fraîcheur |
| Reprise non concluante | Orienter vers un traitement autorisé et identifiable |

La responsabilité de bout en bout ne disparaît pas parce que plusieurs équipes fournissent des composants fiables séparément.

## 7. Sécurité, conformité et acceptation du risque

La sécurité contribue à comprendre les menaces, à définir les contrôles et à évaluer leur réalisation. Les fonctions de conformité et de protection des données interviennent selon les obligations applicables. Leurs responsabilités ne doivent pas être fusionnées dans une case unique intitulée « validation sécurité ».

Pour notre exemple, les questions portent sur les acteurs autorisés à consulter, les opérations administratives sensibles, les données envoyées au partenaire et les traces nécessaires à une investigation. Il faut également préciser les actions interdites, y compris en situation d’urgence.

Une recommandation technique, une approbation d’accès et une acceptation de risque sont des décisions différentes. L’organisation doit définir qui peut les prendre et dans quel périmètre. Une signature sans connaissance du risque ne remplace pas une décision éclairée.

Les textes de gouvernance de The Open Group relient précisément la gestion de l’architecture, les responsabilités et le respect des obligations et principes retenus par l’organisation. [1]

## 8. Rendre les décisions attribuables

Lorsque plusieurs acteurs participent, il faut séparer celui qui prépare une proposition, celui qui contribue, celui qui décide et celui qui réalise. Une décision collective peut avoir une autorité identifiée ; une consultation large n’impose pas l’unanimité.

Pour notre cas, nous proposons les attributions suivantes. Elles sont illustratives et doivent être adaptées au mandat réel des personnes.

| Sujet | Préparation et contribution | Autorité à préciser |
|---|---|---|
| Sens d’un état de paiement | Experts métier, produit, conception | Responsable mandaté pour la règle métier |
| Frontière entre modules | Architecture applicative, tech lead, équipe | Autorité de conception du périmètre |
| Contrat avec le partenaire | Métier, intégration, sécurité, exploitation | Responsable du service et du contrat |
| Capacité et déploiement | Plateforme, application, exploitation | Autorité du périmètre technique |
| Exception à un principe | Équipe concernée et spécialistes | Instance habilitée à accepter l’exception |

Une matrice RACI peut aider à rendre cette répartition lisible. Dans la convention utilisée ici, R désigne la réalisation, A l’autorité qui répond du résultat, C les personnes consultées et I les personnes informées. Cette convention doit être explicitée : les usages locaux du sigle ne sont pas toujours identiques.

Le tableau ne remplace toutefois ni le mandat ni la capacité d’action. Nommer une personne responsable de la reprise sans lui donner accès aux informations nécessaires ne résout pas le problème.

## 9. Organiser un atelier qui produit une décision

Une réunion de conception gagne à partir d’une question précise : comment représenter et traiter une réponse perdue du partenaire ? Les participants doivent connaître le contexte, les informations disponibles et la décision recherchée.

L’atelier peut commencer par une séquence. Le métier décrit le sens des résultats. L’intégration présente ce que le partenaire garantit. L’équipe applicative explique ce qui est persisté. L’exploitation décrit les possibilités de détection et de reprise. La sécurité examine les actions sensibles.

La discussion doit séparer les faits, les hypothèses et les préférences. « Le partenaire reconnaît la même référence » demande une preuve contractuelle ou expérimentale. « Nous préférons un appel synchrone » exprime une option. « Le client ne doit pas recevoir un rejet sans preuve » formule une exigence à confirmer.

À l’issue de l’atelier, il faut pouvoir formuler une décision ou une question de vérification. Si l’information manque, un essai ciblé peut être préférable à une conclusion artificielle.

La gestion des parties prenantes, telle qu’expliquée dans les documents TOGAF de The Open Group, vise notamment à identifier les acteurs et à utiliser leurs préoccupations pour façonner l’architecture et sa communication. [3]

## 10. Arbitrer un désaccord sans masquer le compromis

Supposons que le produit demande un résultat immédiat, que le partenaire réponde parfois tard et que l’exploitation refuse les reprises manuelles non contrôlées. L’architecte ne peut pas supprimer cette contradiction par un diagramme.

Il faut distinguer la réponse immédiate sur la réception de la demande et la confirmation ultérieure du résultat. Une option consiste à exposer un état d’attente explicite avec un mécanisme de consultation. Une autre peut consister à modifier le parcours métier. Les options doivent être comparées selon la valeur, la complexité, les risques et les capacités du partenaire.

La décision doit dire ce qui n’est pas obtenu. Afficher une attente évite une fausse certitude, mais peut augmenter le besoin d’information du client. Un mécanisme de rapprochement réduit certaines interventions, mais demande des données et des procédures.

Le rôle de l’architecte n’est pas de rendre tous les choix agréables. Il est de les rendre compréhensibles et de permettre à l’autorité compétente d’arbitrer.

## 11. Autonomie locale et cohérence d’ensemble

Une équipe doit pouvoir prendre des décisions dans son périmètre sans soumettre chaque détail à une instance centrale. Mais cette autonomie suppose des frontières et des principes partagés.

Dans notre exemple, choisir l’organisation interne d’un adaptateur peut relever de l’équipe. Changer la signification d’un événement utilisé par plusieurs applications nécessite une coordination plus large. Modifier une règle de reprise susceptible de produire un second effet métier concerne également les acteurs responsables de ce risque.

La gouvernance doit donc distinguer les décisions selon leur impact. Le livre blanc de The Open Group sur la gouvernance souligne notamment l’importance de différencier les types de décisions, d’adapter les mécanismes de gouvernance et de tenir compte des processus sociaux qui les rendent effectifs. [4]

La centralisation totale ralentit les changements. L’autonomie sans contrat déplace les problèmes vers les consommateurs. Une organisation cohérente rend explicites les décisions locales, les décisions partagées et les voies de résolution des exceptions.

## 12. Collaborer pendant l’incident et après lui

L’incident révèle les responsabilités réellement exercées. Si plusieurs équipes constatent que leurs composants fonctionnent mais que le client ne peut plus suivre ses paiements, il manque une lecture du service complet.

Pour notre cas fictif, il faut identifier qui coordonne l’analyse, qui peut intervenir sur chaque composant, qui communique et qui valide le retour au fonctionnement attendu. La coordination de l’incident et la recherche technique peuvent être portées par des personnes différentes. Le partage doit être adapté à l’organisation et aux autorisations réelles.

Après le rétablissement, la question d’architecture est de comprendre ce que l’événement révèle : donnée manquante, dépendance non identifiée, contrat ambigu ou procédure insuffisante. La correction ne doit pas s’arrêter au composant qui a présenté le symptôme.

L’expérience de production doit ainsi pouvoir modifier les modèles et les choix. Si le rapprochement nécessite régulièrement une information absente, la conception doit être revue avec le métier et l’intégration.

## 13. Les formes de collaboration qui échouent

L’architecte isolé produit un modèle que l’équipe découvre trop tard. L’équipe isolée optimise son composant sans examiner les consommateurs. La validation tardive de sécurité impose des changements coûteux. L’exploitation sollicitée seulement au déploiement découvre qu’aucune reprise n’est possible.

Une autre difficulté est l’approbation sans contenu. Plusieurs signatures apparaissent, mais personne ne peut expliquer les hypothèses acceptées. À l’inverse, une documentation détaillée sans autorité de décision laisse les désaccords se prolonger indéfiniment.

La collaboration utile ne se mesure ni au nombre de réunions ni au nombre de participants. Elle se reconnaît à des effets concrets : les termes deviennent clairs, les risques ont des responsables et les décisions tiennent compte des personnes qui réaliseront et exploiteront le système.

## 14. Exercice : attribuer une reprise sensible

Le partenaire n’a pas répondu à une demande de paiement. L’assistance veut la renvoyer. L’exploitation constate que l’application est disponible. Le développement indique qu’un bouton de relance peut être ajouté rapidement.

Identifier les questions à poser avant d’autoriser ce bouton. Répartir les contributions entre métier, produit, intégration, développement, sécurité et exploitation. Préciser la décision qui ne doit pas être prise par le développement seul.

Une réponse argumentée commence par vérifier ce que signifie l’absence de réponse et si le partenaire reconnaît une demande répétée. Le métier précise les effets acceptables ; l’intégration établit les garanties du contrat ; le développement propose des mécanismes ; la sécurité encadre les droits ; l’exploitation définit les conditions de surveillance et de reprise ; le produit examine l’usage proposé.

L’acceptation d’un risque de second effet ne découle pas de la facilité à ajouter un bouton. L’autorité habilitée doit être identifiée, et les garanties doivent être démontrées avant de présenter la relance comme sûre.

## 15. Une responsabilité reste entière lorsqu’elle est partagée

L’architecture se construit par contributions complémentaires. Le métier donne le sens, les équipes réalisent les mécanismes, les spécialistes apportent leurs contraintes et les autorités mandatées arbitrent les compromis. L’architecte relie ces réponses et s’assure que leurs interfaces ne laissent pas de responsabilité sans porteur.

Partager le travail ne signifie pas diluer la responsabilité. Il faut savoir qui explique un état, qui protège une règle, qui maîtrise un contrat et qui traite un échec. Ces questions donnent une portée concrète aux représentations étudiées au chapitre précédent.

Le chapitre suivant précisera comment transformer ces préoccupations en exigences et en attributs de qualité vérifiables.

## Références

[1] The Open Group, documentation historique TOGAF, *Architecture Governance*, responsabilité, autorité et organisation de la gouvernance : https://www.opengroup.org/architecture/doc-review/protected-d3/htmlclean/chap48.html

[2] Microsoft Learn, *Designing a DDD-oriented microservice*, modélisation du domaine et responsabilités des couches : https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/ddd-oriented-microservice

[3] The Open Group, documentation historique TOGAF, *Stakeholder Management* : https://www.opengroup.org/architecture/doc-review/protected-sanity/htmlclean/chap24.html

[4] The Open Group, *Governance in IT and Architecture*, livre blanc proposant des compléments à TOGAF 9, et non prescription de l’édition actuelle : https://pubs.opengroup.org/onlinepubs/7698909699/toc.pdf

[Chapitre précédent](chapitre-02.md) · [Sommaire](../SOMMAIRE.md)
