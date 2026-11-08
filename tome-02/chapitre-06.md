# Chapitre 6 — Architecture hexagonale, Clean Architecture et Onion Architecture

## 1. Déplacer la dépendance plutôt que déplacer seulement les classes

Dans une architecture traditionnelle, le métier peut finir par dépendre d’un client HTTP, d’une session de persistance et du format imposé par un partenaire. Le problème n’est pas la présence de ces mécanismes, mais la manière dont ils influencent les décisions du logiciel.

L’architecture hexagonale d’Alistair Cockburn organise la distinction entre l’application et son environnement par des ports et des adaptateurs. Son objectif comprend la possibilité de piloter et tester l’application sans ses dispositifs définitifs d’entrée et de stockage. La forme hexagonale ne prescrit pas six composants. [1]

Pour notre système fictif, le cas d’usage doit pouvoir enregistrer une confirmation sans savoir si elle arrive par HTTP, message ou batch. Il doit aussi exprimer son besoin de persistance sans importer le client concret du stockage.

## 2. Les ports représentent des conversations utiles

Un port entrant expose une capacité de l’application. Un port sortant décrit une interaction dont elle a besoin. Un adaptateur entrant appelle le cœur ; un adaptateur sortant réalise une capacité externe. Plusieurs adaptateurs peuvent correspondre au même port. [1]

```text
HTTP / Batch / Messages
          ↓
Adaptateurs entrants
          ↓
Cas d’usage et domaine
          ↓ utilisent des contrats
Ports sortants
          ↑ réalisés par
Adaptateurs SQL / partenaire / publication
```

Les contrats doivent parler du besoin : charger un paiement, conserver une modification ou rechercher un résultat. Exposer toutes les méthodes d’un client de base de données au travers d’une interface ne protège pas réellement le cœur.

Un port ne correspond pas obligatoirement à une classe distincte pour chaque méthode. Il rassemble une interaction cohérente ; le nombre d’interfaces doit rester proportionné.

## 3. Dépendances du code et flux d’exécution

À l’exécution, le cas d’usage utilise un objet technique. Dans le code, il utilise une interface définie du côté du cœur. L’adaptateur dépend de cette interface pour la réaliser.

```text
Exécution : cas d’usage → adaptateur → stockage
Code : cas d’usage → port ← adaptateur
```

Une interface de persistance peut retourner un agrégat selon les besoins du cœur. Un résultat de pilote SQL ou un objet de requête propre à un framework ne doit pas se propager implicitement dans le modèle.

La composition relie les objets au démarrage. Un assemblage, placé à la périphérie, choisit les adaptateurs et fournit les dépendances. L’injection par constructeur peut être réalisée manuellement ou par un conteneur ; elle ne nécessite pas, par principe, de faire connaître le conteneur au domaine.

## 4. Clean et Onion : des proximités, pas une identité parfaite

Robert C. Martin présente Clean Architecture autour d’une règle de dépendance du code vers l’intérieur. Il distingue règles générales, cas d’usage, adaptateurs d’interface et mécanismes externes, tout en précisant que quatre cercles ne constituent pas un nombre obligatoire. [2]

Jeffrey Palermo place explicitement le modèle de domaine au centre d’Onion Architecture, avec les réalisations d’infrastructure à l’extérieur. Les couches externes peuvent utiliser des couches plus centrales, sans imposer de traverser systématiquement chaque anneau. [3]

| Approche | Accent distinctif |
|---|---|
| Hexagonale | Frontière application-environnement et conversations par ports |
| Clean | Politiques, cas d’usage et règle de dépendance |
| Onion | Modèle de domaine central et infrastructure externalisée |

Le terme Entity de Clean ne doit pas être assimilé automatiquement à l’entité DDD : les vocabulaires répondent à des perspectives différentes. Dans notre réalisation, nous précisons les responsabilités plutôt que de mélanger les noms.

## 5. Organiser les cas d’usage et leurs résultats

Le cas d’usage reçoit une demande adaptée, applique les contrôles nécessaires, charge le modèle, déclenche les règles et coordonne la persistance. Une représentation de réponse évite de publier directement les objets internes.

Clean décrit également le recours à une interface de sortie lorsqu’un cas d’usage doit communiquer avec un présentateur situé à l’extérieur. L’interface permet de préserver la direction des dépendances malgré un flux d’exécution sortant. [2]

Pour notre application Java, un retour direct immuable peut suffire à une consultation simple. Il n’est pas nécessaire d’ajouter un présentateur et une interface de sortie à chaque opération si leur responsabilité n’est pas distincte.

Le contrat doit néanmoins préciser les absences, les erreurs et les faits réellement reconnus. La séparation technique ne corrige pas une définition ambiguë du résultat métier.

## 6. Transactions, autorisation et compromis

Le chapitre précédent utilisait une annotation transactionnelle dans le service applicatif. Cette option introduit une dépendance au framework. Si nous choisissons un cœur strictement indépendant, un décorateur transactionnel externe peut envelopper le cas d’usage.

Dans les deux options, il faut vérifier le comportement effectif : quelles opérations sont validées ensemble et quand la réponse devient-elle durable ? Changer la place d’une annotation ne fournit pas cette preuve.

L’autorisation peut également utiliser un contrat adapté au cœur, avec une réalisation externe. Les règles métier nécessaires à la validité d’une action ne doivent pas être remplacées par un simple rôle technique.

L’isolation n’efface ni les contraintes de concurrence ni les caractéristiques du stockage. Un nouvel adaptateur doit préserver les engagements attendus ; son ajout peut nécessiter une étude de performances et de transactions.

## 7. Vérifier l’architecture par substitution

Un adaptateur en mémoire permet d’examiner un cas d’usage sans démarrer une base. Un adaptateur partenaire simulé permet de représenter des réponses normales, ambiguës ou absentes. Ces essais contrôlent le raisonnement du cœur, pas les garanties de la véritable infrastructure.

Les tests de contrat d’adaptateur et les tests d’intégration doivent compléter cette substitution. Un faux repository qui conserve les objets par référence peut notamment cacher une sauvegarde manquante.

Exercice : remplacer l’adaptateur HTTP par un batch tout en conservant le cas d’usage de confirmation. La correction doit identifier ce qui change dans l’entrée et ce qui reste inchangé dans les règles, les droits et la persistance. Si les règles doivent être recopiées, la frontière est insuffisante.

Ces approches sont utiles lorsque l’isolation protège des comportements importants. Pour une fonctionnalité simple, multiplier les conversions et interfaces sans responsabilité claire peut devenir un coût inutile.

## Références

[1] Alistair Cockburn, *Hexagonal architecture* : https://alistair.cockburn.us/hexagonal-architecture

[2] Robert C. Martin, *The Clean Architecture*, 2012 : https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html

[3] Jeffrey Palermo, *The Onion Architecture: part 1*, 2008 : https://jeffreypalermo.com/2008/07/the-onion-architecture-part-1/

[Précédent](chapitre-05.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-07.md)
