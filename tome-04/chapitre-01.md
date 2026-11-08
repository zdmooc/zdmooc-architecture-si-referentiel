# Chapitre 1 — Concevoir les environnements et les déploiements

## 1. Déployer sans perdre les engagements de la solution

Les tomes précédents ont relié responsabilités, règles et garanties distribuées. Il reste à installer ces mécanismes, à les observer et à les faire évoluer dans des conditions réelles. Une architecture n’est pas terminée lorsque ses composants compilent : elle doit préserver ses engagements pendant un démarrage, un remplacement ou une panne.

Dans notre cas fictif de paiements, remplacer une instance peut interrompre un appel partenaire, laisser une publication à reprendre ou provoquer une nouvelle livraison. Le déploiement doit donc respecter les règles du tome III plutôt que supposer une période sans travail en cours.

Ce tome étudiera plateformes, exploitation et modernisation. Il commence par les environnements et les unités à déployer, avant les particularités Java, middleware et conteneurs. Aucun orchestrateur n’est imposé comme point de départ.

## 2. Définir un environnement par sa fonction

Un environnement rassemble des ressources, des identités, des données et une configuration pour un usage donné. Son nom ne prouve ni son isolement ni sa représentativité.

| Usage | Question principale |
|---|---|
| Développement | Peut-on expérimenter sans affecter les autres usages ? |
| Intégration | Les composants et contrats collaborent-ils correctement ? |
| Qualification | Les exigences ciblées sont-elles vérifiées dans des conditions explicites ? |
| Préproduction | Les procédures et paramètres importants du déploiement sont-ils représentés ? |
| Production | Les engagements réels sont-ils tenus et surveillés ? |

Cette organisation est une proposition, pas un nombre obligatoire d’environnements. Une équipe peut séparer la qualification de performance, créer des environnements temporaires ou mutualiser certains usages si leurs contraintes le permettent.

Microsoft recommande des environnements de préparation représentatifs et des contrôles variés avant exposition progressive. [1] Il faut préciser quelles propriétés sont représentatives : topologie, identité, réseau, données, volumes ou comportement des partenaires. Une préproduction plus petite ne démontre pas automatiquement la capacité de production.

## 3. Isoler les effets, pas seulement les noms

Une file appelée test peut encore conduire à un consommateur de production. Une application de qualification peut utiliser un secret valable sur le partenaire réel. Une base distincte n’empêche pas un batch partagé d’envoyer des demandes hors périmètre.

Pour notre étude, nous proposons des identités, destinations et droits adaptés à chaque environnement. Les composants qui produisent des effets externes doivent être examinés ensemble : clients, traitements différés, relais Outbox, consommateurs et outils opérateurs.

Les contrôles doivent empêcher les flux interdits, pas seulement demander aux développeurs d’éviter certaines URL. Les données de test doivent couvrir les scénarios utiles sans recopier par défaut des informations personnelles. L’usage éventuel de données réelles demande une justification et des protections propres au contexte.

Les environnements temporaires doivent aussi avoir un responsable et une procédure de suppression : retirer l’application sans supprimer comptes, files ou données peut laisser des accès et des coûts persistants.

## 4. Identifier ce qui est réellement installé

Une version source, un binaire et une configuration ne sont pas la même chose. Nous proposons de distinguer l’identité du code, celle de l’artefact construit, la configuration appliquée et les versions de dépendances pertinentes.

Le modèle de provenance SLSA décrit comment un artefact a été produit, à partir d’une définition de construction et d’une plateforme identifiée. Son intérêt est notamment de permettre au consommateur de vérifier une construction selon ses attentes. Une provenance doit être vérifiée dans un modèle de confiance ; sa présence ne certifie pas la correction fonctionnelle. [2]

```text
Sources identifiées → construction contrôlée → artefact identifié
                                             ↓
                     promotion du même artefact
                        intégration → qualification → production

Configuration et références de secrets : propres à chaque environnement
```

Promouvoir le même artefact évite de qualifier un binaire puis d’en produire silencieusement un autre pour la production. Cela n’impose pas d’utiliser les mêmes secrets ni les mêmes valeurs de capacité partout.

Une étiquette de version réaffectable ne suffit pas à prouver l’identité des octets. Le dispositif doit permettre de retrouver l’artefact exact et les preuves associées. Une modification de configuration doit également être contrôlée : elle peut changer le comportement sans changer le code. [1]

## 5. Décrire les unités et les dépendances

Le modèle logique n’est pas automatiquement le modèle de déploiement. Un module peut rester dans le même processus ; un relais peut être exécuté séparément pour maîtriser ses ressources et son cycle de vie.

Pour notre solution, nous examinons l’API, les traitements de transmission, les relais de publication, les consommateurs et les outils d’investigation. Le stockage et le broker peuvent être administrés par une autre équipe ou un service externe : leur responsabilité doit rester explicite.

La description de déploiement associe à chaque unité son artefact, son identité, ses ressources, ses points d’entrée, ses destinations et son état durable. Elle précise aussi démarrage, arrêt, montée en capacité et comportement en cas d’indisponibilité.

Des dépendances de démarrage ne doivent pas devenir une séquence manuelle fragile. Notre application doit définir ce qu’elle peut servir lorsqu’une dépendance manque, ce qu’elle refuse et ce qu’elle conserve pour reprise.

## 6. Choisir comment les versions se remplacent

| Stratégie | Organisation | Vigilance |
|---|---|---|
| Arrêt puis remplacement | Ancienne version arrêtée avant ouverture de la nouvelle | Interruption et traitement du travail restant |
| Remplacement progressif | Anciennes et nouvelles instances coexistent | Compatibilité et capacité pendant la transition |
| Blue-green | Deux ensembles avec une bascule de routage | Données partagées, capacité et retour possible |
| Canary | Exposition limitée avant extension | Population observée, critères et durée d’observation |

Ces stratégies peuvent se combiner. Microsoft présente l’exposition progressive comme un moyen de limiter le périmètre affecté et recommande d’arrêter la progression lorsque les indicateurs révèlent un problème. [1]

La réussite d’une petite population ne prouve pas tous les parcours. Une opération rare, un batch de fin de journée ou un consommateur peu actif peut ne pas avoir été observé. Les critères doivent inclure activité réelle et comportements métier, pas seulement absence d’alertes.

Deux ensembles blue-green peuvent partager une base ou un partenaire. Leur séparation de calcul ne crée donc pas deux réalités métier indépendantes. Les consommateurs et tâches planifiées ne doivent pas devenir actifs en double par accident.

## 7. Préserver la compatibilité pendant la coexistence

Une nouvelle version peut produire un champ ou un message que l’ancienne ne comprend pas. Revenir à l’ancien binaire ne corrige pas automatiquement les données déjà écrites.

Nous proposons d’étudier séparément API, événements, schéma de stockage, configuration et travail différé. Les messages anciens encore en file font partie du patrimoine à interpréter ; les messages nouveaux peuvent survivre au retrait du producteur.

Une trajectoire d’extension, migration puis retrait permet d’organiser certaines évolutions : introduire une représentation compatible, migrer et vérifier, puis supprimer seulement après disparition des usages anciens. Ce séquencement doit être validé pour le changement considéré.

Une sauvegarde n’est pas un rollback applicatif universel. Restaurer des données locales peut perdre des faits récents alors que le partenaire a déjà produit leurs effets. Microsoft souligne la difficulté des retours arrière touchant données et schémas. [1] Notre décision doit comparer retour du code, correction en avant et restauration avec rapprochement.

## 8. Distinguer processus vivant, prêt et service correct

Kubernetes distingue les sondes de démarrage, de vivacité et de disponibilité au trafic. Une sonde de vivacité peut conduire au redémarrage ; une sonde de readiness échouée retire normalement le Pod des endpoints prêts des Services concernés. Ces mécanismes n’ont pas le même objectif. [3]

Faire dépendre la vivacité d’un partenaire indisponible peut provoquer des redémarrages sans réparer la cause. Pour notre étude, les sondes doivent représenter une action utile : redémarrer ce processus, suspendre de nouvelles entrées ou poursuivre en mode limité.

Une API prête à servir une consultation ne prouve pas que le relais Outbox progresse. Un déploiement nécessite donc aussi des signaux sur le travail différé : ancienneté des attentes, publications bloquées et situations à résoudre.

Kubernetes permet de borner les indisponibilités et les instances supplémentaires pendant un RollingUpdate. Cela ne démontre pas, seul, une absence d’interruption de bout en bout. Capacité, routage, compatibilité et dépendances doivent être qualifiés ensemble. [4]

## 9. Arrêter proprement sans dépendre d’un arrêt parfait

Lors d’un remplacement, l’unité doit cesser de prendre de nouveaux travaux, terminer ce qui peut l’être dans un délai borné et laisser une reprise durable pour le reste.

Kubernetes prévoit une terminaison gracieuse avec une période limitée, après laquelle l’arrêt peut être forcé. La propagation du retrait du trafic et le traitement des connexions doivent être examinés dans la chaîne réelle. [3]

Un consommateur ne doit pas acquitter un effet non validé simplement pour sortir rapidement. Un relais peut avoir envoyé sans enregistrer son avancement ; une livraison répétée doit donc rester sûre. Un appel partenaire interrompu peut rester inconnu, même si le processus s’est arrêté proprement.

La robustesse doit aussi couvrir arrêt brutal et perte de machine. Un hook d’arrêt améliore certains parcours ; il ne constitue pas l’unique protection des données ni des effets.

## 10. Préparer le contrôle et la récupération

Avant ouverture, nous proposons de vérifier l’identité de l’artefact, la compatibilité, les droits et destinations, la capacité transitoire, les signaux de santé et les conditions de récupération.

| Moment | Preuve recherchée |
|---|---|
| Avant déploiement | Artefact attendu et préconditions satisfaites |
| Pendant remplacement | Capacité et travail en cours maîtrisés |
| Pendant observation | Parcours exercés et indicateurs interprétables |
| Après incident | Arrêt de progression et récupération compatibles avec les faits |

Un pourcentage d’erreurs stable peut masquer une chute des demandes acceptées. La santé doit rapprocher activité, erreurs, délai et résultat utile. Microsoft recommande précisément d’inclure les métriques d’usage dans le modèle de santé. [1]

Les responsabilités doivent permettre de décider qui suspend, qui diagnostique et qui autorise une reprise sensible. Le chapitre consacré à l’industrialisation approfondira ces procédures et leurs automatisations.

## 11. Exercice : le retour arrière qui ne revient pas

Une nouvelle version renomme un statut en base et publie un nouvel événement. Le déploiement progressif conserve d’anciennes instances. Après une erreur, l’équipe réinstalle simplement le binaire précédent.

Identifier les incompatibilités possibles et proposer une trajectoire plus sûre.

La correction examine lecture et écriture de chaque version, consommation des deux contrats et persistance des messages après rollback. Elle propose une coexistence compatible, des contrôles et une décision explicite entre retour du code et correction en avant.

Elle vérifie enfin le remplacement pendant un appel partenaire et après un commit consommateur non acquitté. La récupération doit préserver les faits reconnus et les travaux à reprendre. Les essais proposés ici n’ont pas été exécutés ; ils définissent les preuves attendues.

## 12. Concevoir un système que l’on peut faire évoluer

Les environnements rendent certaines hypothèses vérifiables. Les artefacts identifiés relient construction et exécution. Les stratégies de remplacement organisent l’exposition au changement ; elles ne remplacent ni la compatibilité ni les garanties de reprise.

Une solution exploitable doit rester compréhensible lorsque plusieurs versions coexistent et lorsque l’arrêt n’est pas idéal. Le chapitre suivant examinera les dépendances Java et middleware qui influencent ces possibilités : runtime, bibliothèques, serveur d’applications et services techniques.

## Références

[1] Microsoft Learn, *Architecture strategies for safe deployment practices* : https://learn.microsoft.com/en-us/azure/well-architected/operational-excellence/safe-deployments

[2] SLSA, *Provenance*, documentation du modèle dans la spécification 1.0 ; référence de schéma, non affirmation de version courante : https://slsa.dev/spec/v1.0/provenance

[3] Kubernetes, *Pod Lifecycle*, sondes et terminaison : https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/

[4] Kubernetes, *Deployments*, remplacement progressif et conditions de progression : https://kubernetes.io/docs/concepts/workloads/controllers/deployment/

[Fin du tome III](../tome-03/chapitre-14.md) · [Sommaire](../SOMMAIRE.md)
