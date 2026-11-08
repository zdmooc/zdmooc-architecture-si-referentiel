# Chapitre 12 — Concevoir une solution fictive de paiements de bout en bout

## 1. Fixer le problème et les limites

Une organisation fictive veut recevoir des demandes, les soumettre à un partenaire d’exécution et présenter le dernier résultat connu. L’assistance doit pouvoir rechercher une situation incertaine sans modifier librement les faits.

Le périmètre ne comprend pas le fonctionnement interne du transfert externe ni un système bancaire complet. Les règles qui suivent sont proposées pour cette étude, pas comme une norme de paiement.

Les objectifs sont la compréhension du résultat, la maîtrise des répétitions et la possibilité de reprise. Le choix technique doit être relié à ces objectifs et aux preuves définies au chapitre précédent.

## 2. Choisir une structure initiale

Une équipe porte le premier périmètre. Aucun besoin démontré n’impose encore des déploiements applicatifs indépendants. Nous retenons donc une application modulaire, organisée avec un cœur métier et des adaptateurs.

```text
Clients et assistance
          ↓
Adaptateurs d’entrée et sécurité
          ↓
Application modulaire
├── Paiements : règles et faits reconnus
├── Résolution : investigations et coordination
├── Consultation : représentations de lecture
└── Notifications : information et livraison
          ↓
Ports sortants
├── Persistance transactionnelle
├── Adaptation du partenaire
└── Publication et livraison
```

Les modules ne sont pas automatiquement quatre contextes DDD. La relation entre Paiements et Résolution reste à examiner selon la cohésion des règles. La consultation peut être une responsabilité de lecture dans le même contexte.

L’architecture hexagonale permet plusieurs adaptateurs d’entrée et de sortie. Elle protège les règles, sans rendre les garanties du stockage ou du partenaire interchangeables par simple déclaration. [1]

## 3. Séparer les dimensions de l’état

Un champ unique qui signifie alternativement reçu, en cours, erreur et terminé reste insuffisant. Nous proposons de distinguer trois dimensions.

| Dimension | Exemples de valeurs pour l’étude |
|---|---|
| Prise en charge locale | Reçue, validée, rejetée localement |
| Coordination de la soumission | À transmettre, interaction engagée, clarification nécessaire |
| Résultat reconnu | Indéterminé, exécution confirmée, rejet établi |

Ces valeurs ne sont pas toutes combinables. Les règles doivent définir les associations valides et les transitions autorisées.

Une interaction engagée indique un jalon local de coordination, pas une preuve que le partenaire a exécuté l’action. Si le processus s’arrête entre le jalon durable et l’envoi réel, la reprise doit traiter cette incertitude.

L’agrégat minimal du chapitre 3 illustrait une partie des règles. Il ne peut pas être réutilisé tel quel comme modèle complet de cette étude.

## 4. Établir le contrat de prise en charge

Une demande comporte une identité d’intention dans un périmètre défini, un contenu contrôlé et les informations nécessaires à son traitement. L’utilisateur doit être autorisé à agir sur les ressources concernées.

Le premier traitement valide la forme, applique les règles locales et conserve la demande avec une référence stable et le travail de transmission à réaliser. La reconnaissance d’une répétition doit être coordonnée avec la création durable.

La réponse expose ce qui est réellement acquis : réception ou création de la ressource, non confirmation d’exécution. HTTP distingue notamment création et acceptation pour traitement. [2]

| Répétition | Comportement proposé |
|---|---|
| Même intention et même contenu reconnu | Retrouver la demande existante |
| Même clé et contenu incompatible | Refuser le conflit selon le contrat |
| Nouvelle intention légitime | Créer une demande distincte |

La durée de reconnaissance et les règles de rétention font partie du contrat. Une clé ne peut pas être supprimée sans examiner les répétitions tardives possibles.

## 5. Coordonner la soumission et l’incertitude

Un traitement de transmission retrouve le travail durable et utilise la référence prévue. La politique doit gérer les accès concurrents aux travaux et empêcher des envois non maîtrisés.

Avant d’automatiser une nouvelle tentative, il faut établir ce que le partenaire garantit : reconnaissance d’une référence, interrogation du résultat ou protocole de répétition.

Si la réponse manque, le système ne doit pas inventer un rejet. Il conserve la connaissance disponible et organise une recherche. L’absence de correspondance à un instant donné ne prouve pas nécessairement qu’une ancienne tentative ne produira jamais d’effet.

Si le contrat ne permet pas une résolution automatique sûre, le processus prévoit une intervention habilitée. La solution ne doit pas promettre une automatisation qu’elle ne peut justifier.

Un résultat tardif doit être corrélé et interprété. Une contradiction avec un fait déjà reconnu doit déclencher un traitement explicite, pas une simple écriture de la dernière valeur reçue.

## 6. Persister et publier sans confondre les garanties

La confirmation reconnue modifie l’état selon les règles. Le contrôle de version ou le mécanisme retenu arbitre les écritures concurrentes.

Nous proposons de conserver dans la même transaction locale l’évolution et une publication à transmettre. Le relais reprend les publications en attente ; le consommateur doit traiter leur répétition éventuelle.

```text
Transaction locale : fait reconnu + publication durable
                         ↓
Relais de publication
                         ↓
Notification ou autre consommateur
```

Cette proposition ne fournit pas une exécution unique de tout effet externe. Le tome III examinera précisément les propriétés d’Outbox, de déduplication et de reprise.

Le niveau d’isolation doit être choisi selon les règles et testé sur le moteur retenu. La présence d’une transaction n’élimine pas toutes les anomalies possibles. [3]

## 7. Exposer la consultation et protéger les interventions

Le client voit le dernier résultat connu, les informations autorisées et une explication adaptée de l’attente. La date affichée doit désigner un événement précis : dernier fait reconnu ou actualisation de la projection.

Si une projection est utilisée, sa fraîcheur et sa reconstruction sont définies. Une panne de notification ne doit pas modifier silencieusement le fait d’exécution confirmé.

Les fonctions d’assistance distinguent lire, ouvrir une investigation et effectuer une intervention sensible. L’autorisation porte sur l’action et l’objet, sans dépendre seulement de la présence d’un bouton. [4]

La trace d’une intervention conserve ce qui est nécessaire à son explication, sans recopier inutilement les données sensibles. Les durées et obligations de conservation devront être établies pour le contexte réel.

## 8. Relier exigences et preuves

Nous reprenons l’exemple fictif de consultation sous charge du tome I, non comme une mesure acquise mais comme un objectif à préciser et qualifier. Le jeu de données, la durée, la population admissible et le point de mesure doivent être fixés avant l’essai.

| Propriété attendue | Scénario de qualification |
|---|---|
| Répétition maîtrisée | Même intention reçue séquentiellement puis simultanément |
| Confirmation cohérente | Résultats identiques puis contradictoires |
| Reprise possible | Arrêt avant envoi, après réception distante et après écriture |
| Publication récupérable | Arrêt après publication avant marquage local |
| Accès correct | Consultation et reprise hors périmètre refusées |
| Service exploitable | Retard détecté et cas non résolus attribués |

Les objectifs de restauration doivent aussi examiner les effets déjà produits chez le partenaire. Une restauration locale réussie ne démontre pas leur alignement.

Les essais décrits n’ont pas été exécutés dans cette étude. Le chapitre fournit une conception argumentée et un programme de qualification, pas un bilan de production.

## 9. Décider la trajectoire

Le premier incrément peut couvrir réception, consultation et recherche encadrée. L’ouverture de reprises automatiques dépend des garanties établies avec le partenaire.

Une extraction ultérieure de Notifications doit être motivée par un besoin réel d’autonomie ou de capacité. Elle exige alors contrats de messages, sécurité, observation et reprise adaptés à la distribution.

Dans un patrimoine existant, la coexistence doit déterminer qui écrit, comment les références anciennes sont retrouvées et quand l’outil remplacé peut être retiré. Une nouvelle interface ne suffit pas à fermer les usages historiques.

Les décisions à préserver sont les motifs du déploiement commun, les limites d’automatisation, les autorités de données et les conditions d’extraction. Elles permettent d’évaluer les changements sans reconstruire tout le raisonnement.

## 10. Conclusion du tome II

La conception d’une solution relie des engagements à des responsabilités, puis à des modèles, des contrats et des mécanismes. Les styles architecturaux donnent des moyens de structurer ces choix, mais ne fournissent pas leur justification.

DDD aide à représenter les règles. La modularité et les couches organisent le logiciel. Les ports et adaptateurs contrôlent ses dépendances. Les API rendent les engagements accessibles. Les données, la sécurité et les tests établissent les conditions réelles de correction.

Le tome III approfondira les collaborations distribuées : messages, événements, cohérence, idempotence, publication fiable et processus de reprise. Le cas étudié fournit leurs questions d’entrée, sans prétendre les avoir toutes résolues.

## Références

[1] Alistair Cockburn, *Hexagonal architecture* : https://alistair.cockburn.us/hexagonal-architecture

[2] IETF, *RFC 9110 — HTTP Semantics* : https://www.rfc-editor.org/rfc/rfc9110.html

[3] PostgreSQL, *Transaction Isolation* : https://www.postgresql.org/docs/current/transaction-iso.html

[4] OWASP, *Authorization Cheat Sheet* : https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html

[Précédent](chapitre-11.md) · [Sommaire](../SOMMAIRE.md)
