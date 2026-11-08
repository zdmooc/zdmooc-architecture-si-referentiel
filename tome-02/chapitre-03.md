# Chapitre 3 — DDD tactique : entités, objets-valeur et agrégats

## 1. Faire porter les règles par le modèle

Le chapitre précédent a défini les frontières dans lesquelles un modèle possède un sens cohérent. À l’intérieur d’un contexte, il faut maintenant organiser les objets et les comportements : qu’est-ce qui conserve une identité, qu’est-ce qui représente une valeur et quelles règles doivent rester vraies ensemble ?

La conception tactique fournit des outils pour répondre à ces questions. Elle ne consiste pas à appliquer la même structure à toutes les tables. Une classe comportant un identifiant, des attributs et des setters n’exprime pas nécessairement un modèle métier.

Evans présente entités, objets-valeur, services, événements, agrégats, repositories et factories comme des moyens de relier les connaissances du domaine à leur réalisation. Microsoft précise que les patterns tactiques affinent le modèle à l’intérieur des contextes. [1][2]

Nous allons construire un exemple limité de confirmation de paiement. Il illustre la conception ; il n’exécute aucun transfert financier et ne constitue pas un système de production.

## 2. Commencer par les invariants

Avant les classes, décrivons les règles. Pour notre exercice fictif, une demande possède une identité stable, un montant strictement positif et une connaissance de son avancement. Une confirmation doit être associée à une référence non vide et ne peut pas contredire une confirmation déjà enregistrée.

Ces règles sont des hypothèses pédagogiques à valider pour chaque domaine réel.

Un invariant est une propriété qui doit rester vraie après les opérations autorisées. La racine d’agrégat et les entités participent à cette préservation. Les contrôles de formulaire ne suffisent pas : un autre chemin d’entrée peut les contourner. [3]

| Règle de l’exercice | Portée à examiner |
|---|---|
| Montant strictement positif | Objet-valeur utilisé pour cette demande |
| Identité stable | Entité Paiement |
| Confirmation non contradictoire | Agrégat Paiement |
| Droits de l’appelant | Cas d’usage et mécanismes d’autorisation |
| Absence de modification concurrente perdue | Transaction et persistance |

Le tableau montre une distinction essentielle : toutes les propriétés utiles ne sont pas réalisées par une méthode métier isolée.

## 3. L’entité : une continuité d’identité

Une entité est définie par son identité et sa continuité à travers le temps. Deux représentations peuvent désigner la même entité malgré des attributs différents. Deux objets aux attributs identiques peuvent représenter des entités distinctes. [1][2]

Deux demandes de même montant, issues du même acteur, ne doivent donc pas être fusionnées sur la seule égalité de leurs propriétés. À l’inverse, un paiement qui évolue de créé à soumis conserve son identité.

L’identité doit être choisie selon le modèle. Un identifiant technique peut convenir sans résoudre automatiquement l’identification d’une même intention reçue plusieurs fois. La référence du partenaire, l’identifiant de message et l’identité locale peuvent avoir des rôles différents.

Il faut documenter ces rôles et leurs correspondances. Ajouter un UUID à chaque réception ne suffit pas à reconnaître une demande métier répétée.

Enfin, identité d’entité et égalité d’objets dans un langage ne sont pas la même décision. Les conventions d’égalité doivent tenir compte des identifiants, de leur attribution et des usages de persistance.

## 4. L’objet-valeur : exprimer un concept sans identité propre

Un objet-valeur se définit par ses valeurs et le sens qu’elles portent. Deux valeurs équivalentes sont interchangeables dans le modèle. Evans recommande de les traiter comme immuables et de leur associer les opérations pertinentes. [1]

Une devise, une période ou un montant peuvent être de bons candidats. Le choix reste contextuel : une adresse peut être une valeur dans un usage, alors qu’un autre usage doit suivre l’identité d’un enregistrement d’adresse. [2]

Un objet-valeur n’est pas seulement un emballage destiné à multiplier les classes. Il devient utile lorsqu’il porte une signification, des contraintes ou des opérations qui évitent une interprétation répétée.

Il ne faut pas déduire son absence d’identité métier de sa représentation physique. Une table de stockage peut posséder une clé sans transformer automatiquement le concept du domaine en entité.

## 5. Un montant spécialisé plutôt qu’une monnaie universelle

Pour limiter le code, nous supposons que l’exercice accepte uniquement des montants positifs en euros, représentés en centimes entiers. Ce choix n’est pas une recommandation universelle pour les systèmes financiers.

Le fragment suivant utilise un record Java. Chaque type public correspondrait à son propre fichier. Il sert d’illustration et n’a pas été compilé ni exécuté dans ce chapitre.

```java
public record MontantEuroPositif(long centimes) {
    public MontantEuroPositif {
        if (centimes <= 0) {
            throw new IllegalArgumentException(
                "Un montant strictement positif est requis");
        }
    }
}
```

Le nom exprime la restriction. Il évite de faire passer le type pour un modèle multidevise, un solde ou un montant comptable signé.

Une conception complète doit encore préciser les bornes autorisées, les conversions, l’arrondi et les opérations arithmétiques. Une addition devrait notamment traiter le dépassement de capacité de la représentation choisie. Accepter un entier ne suffit pas à établir une politique monétaire.

## 6. L’agrégat : une frontière de cohérence

Un agrégat regroupe une racine et les éléments dont les règles doivent être préservées ensemble. Il peut se limiter à une seule entité. La racine constitue le point d’entrée des modifications qui concernent cette frontière. [1][2][4]

Le choix ne doit pas partir de toutes les associations possibles entre tables. Un paiement peut référencer un compte et un bénéficiaire sans que l’ensemble de leurs données doive appartenir au même agrégat.

```text
Agrégat Paiement
├── Racine : Paiement
├── Montant : objet-valeur
├── État reconnu localement
└── Référence de confirmation, lorsque présente

Autres concepts référencés si nécessaire : par identité
```

Dans notre exercice, la référence de confirmation et l’état confirmé doivent être cohérents. Autoriser leur modification indépendante créerait une situation invalide.

La taille de l’agrégat doit suivre cette nécessité de cohérence. Trop grand, il peut faire entrer des opérations indépendantes en concurrence. Trop fragmenté, il peut répartir une règle indivisible entre des composants incapables de la garantir.

## 7. Concevoir la racine avec des intentions explicites

L’exemple réduit le cycle à créé, soumis et confirmé. Les rejets, annulations et investigations restent hors de ce fragment ; ils doivent être modélisés avant tout usage réel.

```java
import java.util.Objects;
import java.util.UUID;

public final class Paiement {
    public enum Etat { CREE, SOUMIS, CONFIRME }

    private final UUID id;
    private final MontantEuroPositif montant;
    private Etat etat = Etat.CREE;
    private String referenceConfirmation;

    private Paiement(UUID id, MontantEuroPositif montant) {
        this.id = Objects.requireNonNull(id);
        this.montant = Objects.requireNonNull(montant);
    }

    public static Paiement creer(
            UUID id, MontantEuroPositif montant) {
        return new Paiement(id, montant);
    }

    public void marquerSoumis() {
        if (etat != Etat.CREE) {
            throw new IllegalStateException(
                "La demande ne peut pas être marquée soumise");
        }
        etat = Etat.SOUMIS;
    }

    public boolean confirmer(String reference) {
        if (reference == null || reference.isBlank()) {
            throw new IllegalArgumentException(
                "Une référence est requise");
        }
        if (etat == Etat.CONFIRME) {
            if (reference.equals(referenceConfirmation)) {
                return false;
            }
            throw new IllegalStateException(
                "Confirmation contradictoire");
        }
        if (etat != Etat.SOUMIS) {
            throw new IllegalStateException(
                "La demande doit être soumise");
        }
        referenceConfirmation = reference;
        etat = Etat.CONFIRME;
        return true;
    }

    public UUID id() { return id; }
    public MontantEuroPositif montant() { return montant; }
    public Etat etat() { return etat; }
}
```

Le cas d’usage doit fournir une référence reconnue selon le contrat externe. La méthode ne prouve ni la provenance de l’entrée ni la réalité d’un transfert distant.

Le booléen indique seulement si cette instance a changé. Il ne constitue pas une garantie distribuée de traitement unique. La répétition identique est ignorée dans ce modèle réduit ; ce choix doit être validé selon la sémantique du domaine.

## 8. Ce que l’encapsulation protège — et ce qu’elle ne protège pas

La racine empêche l’appelant de changer directement l’état et la référence. Ses méthodes appliquent des transitions exprimées dans le vocabulaire de l’exercice.

Il faut toutefois examiner les autres accès. Un mapping qui expose les champs à tous les appelants, un traitement SQL direct ou une collection interne mutable peuvent contourner la frontière.

Les entités internes d’un agrégat ne doivent pas être modifiées librement depuis l’extérieur. La racine doit pouvoir préserver les règles communes, conformément à la responsabilité décrite par les modèles DDD. [4]

Cette encapsulation ne résout pas à elle seule les écritures concurrentes. Deux instances de Paiement peuvent être chargées depuis le même état et valider chacune une opération incompatible avec l’autre. La persistance doit alors arbitrer les modifications.

## 9. Préserver un état valide en cas d’échec

Une méthode peut changer plusieurs propriétés puis découvrir une erreur. Si elle échoue après une partie des mutations, l’objet peut rester incohérent.

La documentation Microsoft recommande de concevoir les validations et modifications de manière à ne pas laisser une entité dans un état invalide après l’échec d’une opération. [3]

Dans notre fragment, la référence est validée et les transitions sont vérifiées avant la mutation. Cette propriété locale ne doit pas être confondue avec le résultat d’une transaction de stockage.

Si la sauvegarde échoue, l’objet en mémoire peut déjà être modifié. Le cas d’usage doit traiter cet échec, abandonner ou recharger l’état selon sa stratégie, et ne pas publier un succès métier comme si l’écriture avait abouti.

Le contrôle de validité doit aussi intervenir lors de la création ou de la reconstruction d’un agrégat. Charger des données existantes ne doit pas rendre leur cohérence supposée invisible.

## 10. Lier l’agrégat à la concurrence

Une stratégie de concurrence optimiste peut associer une version à l’état chargé. L’écriture vérifie que cette version n’a pas changé. Si un autre traitement a modifié l’état, l’opération doit traiter le conflit. Microsoft illustre ce mécanisme par un jeton de concurrence dans sa documentation de persistance. [5]

Un exemple SQL conceptuel serait :

```sql
UPDATE paiement
SET etat = :nouvel_etat,
    reference_confirmation = :reference,
    version = version + 1
WHERE id = :id
  AND version = :version_lue;
```

Une modification nulle peut notamment indiquer un conflit ou une disparition de la ligne. Le traitement doit distinguer les situations utiles au contrat ; il ne doit pas forcer une écriture en supprimant simplement le contrôle.

Une nouvelle tentative exige de relire les informations et de réévaluer la règle. Répéter aveuglément une action externe n’est pas équivalent à rejouer une décision locale.

Cette esquisse ne fournit pas un mapping de production. Le niveau d’isolation, les contraintes, le stockage des éléments associés et les transactions doivent être qualifiés avec la technologie choisie.

## 11. Factory et repository : création et accès au modèle

Une factory encapsule une création suffisamment complexe pour masquer l’assemblage tout en produisant un objet valide. Une méthode de création simple peut suffire ; une classe Factory n’est pas obligatoire pour chaque entité. [1]

Dans notre code, `creer` exprime le point d’entrée de la création. Une reconstruction depuis le stockage devrait conserver l’identité et l’état existants, sans rejouer artificiellement une création ni produire à nouveau ses effets.

Le repository fournit un accès aux agrégats, avec une interface qui préserve leur frontière. Evans le distingue de la factory : l’un retrouve les objets existants, l’autre organise leur création. [1]

Il ne doit pas être conçu automatiquement comme un CRUD public pour chaque table. Un repository Paiement peut charger et sauvegarder la racine sans exposer une méthode permettant de changer sa référence en dehors de ses règles.

La forme du repository ne garantit pas la transaction. Son contrat et son implémentation doivent préciser comment sauvegarde et concurrence sont traitées.

## 12. Service de domaine ou service applicatif ?

Une règle métier qui ne trouve pas naturellement sa place dans une entité ou un objet-valeur peut relever d’un service de domaine. Le service applicatif orchestre un cas d’usage et les interactions nécessaires. [1][2]

Une politique de choix entre plusieurs possibilités peut être métier. Charger un paiement, appeler cette politique et demander sa sauvegarde relève de l’orchestration.

| Traitement | Place à examiner |
|---|---|
| Refuser une transition contradictoire | Racine Paiement |
| Vérifier une valeur locale | Objet-valeur ou entité concernée |
| Appliquer une règle entre concepts | Service de domaine si nécessaire |
| Charger, coordonner et persister | Service applicatif |
| Appeler HTTP ou exécuter SQL | Adaptateur d’infrastructure |

Créer un service de domaine ne justifie pas d’y déplacer toutes les règles des entités. À l’inverse, faire entrer tout le déroulement technique dans l’agrégat nuit à sa lisibilité.

Il faut chercher l’endroit où la responsabilité a un sens, pas celui où il est le plus rapide d’ajouter une condition.

## 13. Règles entre agrégats

Un processus peut traverser plusieurs agrégats sans qu’ils appartiennent à une même frontière de cohérence. Les recommandations tactiques Microsoft privilégient des agrégats petits et des coordinations explicites entre eux. [2]

Il ne faut cependant pas transformer cette recommandation en abandon des règles. Si une propriété exige une cohérence immédiate entre plusieurs éléments, il faut examiner la frontière retenue et la stratégie transactionnelle avant de décider que la cohérence différée suffit.

Dans notre cas, la livraison d’une notification peut être séparée de la confirmation du paiement si les exigences l’autorisent. L’échec de livraison ne change pas nécessairement le fait déjà établi.

Le processus doit alors suivre ce qui reste à faire. La séparation des agrégats ne fait pas disparaître les reprises ni la responsabilité de bout en bout. Les mécanismes d’intégration et de compensation seront approfondis dans le tome III.

## 14. Les événements expriment des faits, pas une transaction déjà gagnée

Un événement de domaine représente un fait significatif. Il peut rendre explicite une évolution que d’autres comportements doivent connaître. [1][2]

Dans notre modèle, la première confirmation reconnue peut produire un fait à traiter. La répétition identique, qui n’apporte aucun changement selon notre règle, ne doit pas être confondue avec une nouvelle confirmation.

Il faut distinguer l’événement construit dans le modèle, son enregistrement durable et sa publication vers d’autres composants. Un objet créé en mémoire ne démontre pas que la transaction est validée.

La documentation Microsoft distingue également les événements internes et les événements d’intégration publiés après la validation de la transaction d’origine. [2] La fiabilité de cette publication exige encore un mécanisme adapté ; le simple ordre « sauvegarder puis envoyer » laisse une question de défaillance entre les deux étapes.

Le domaine fournit le sens du fait. La réalisation doit en assurer la conservation et la circulation selon les garanties retenues.

## 15. Éprouver les règles avec des scénarios précis

Le fragment Java permet de définir une première série d’essais. Le tableau décrit ce qu’il faudrait vérifier ; il ne présente pas des tests déjà exécutés.

| Situation | Résultat attendu dans l’exercice |
|---|---|
| Créer un montant nul ou négatif | Refus |
| Confirmer une demande créée | Refus sans mutation |
| Soumettre puis confirmer | État confirmé |
| Répéter la même confirmation | Aucun nouveau changement local |
| Fournir une référence différente après confirmation | Conflit explicite |
| Sauvegarder après une modification concurrente | Conflit traité par la persistance |
| Échouer pendant la sauvegarde | Aucun succès durable annoncé à tort |

Les premiers essais peuvent être unitaires. Les derniers exigent des tests d’intégration et des scénarios de reprise avec le stockage réel.

Il faut également tester les absences : aucun setter public ne doit permettre de contourner la transition. Aucune réponse externe ne doit produire une confirmation sans interprétation du contrat.

La preuve doit rester alignée avec sa portée. La réussite d’un test sur une instance en mémoire ne valide pas le traitement concurrent de plusieurs instances en production.

## 16. Changer de domaine sans conserver les mêmes agrégats

Dans une déclaration de sinistre fictive, un dossier peut posséder une identité et un ensemble de pièces. Faut-il inclure toutes les pièces dans son agrégat ? La réponse dépend des règles à préserver immédiatement, de leur cycle de vie et du volume.

Si la complétude doit être évaluée sur un ensemble cohérent, il faut savoir comment cette connaissance est établie et maintenue. Si les documents sont stockés séparément, leur disponibilité technique ne doit pas être confondue avec leur validation métier.

Ce cas ne prescrit pas un découpage assurantiel universel. Il illustre la même question que pour les paiements : quelle information et quelles règles doivent être cohérentes ensemble ?

Il serait incorrect de reprendre automatiquement la structure d’une demande de paiement parce que les deux objets possèdent un identifiant et un statut.

## 17. Reconnaître les mauvais agrégats

Un agrégat qui charge tout le domaine devient difficile à modifier et peut créer une contention inutile. Un agrégat qui ne protège aucune règle peut être seulement un objet de transport renommé.

Une liste interne accessible et mutable permet aux appelants de contourner la racine. Des règles éparpillées dans les contrôleurs et les traitements batch risquent de diverger. Un repository qui modifie les sous-éléments directement contredit la frontière annoncée.

La conception doit aussi éviter de rendre le modèle indépendant de la réalité du stockage au prix d’hypothèses impossibles. L’isolation du domaine protège le sens des règles ; elle ne garantit pas que n’importe quel mapping puisse les réaliser efficacement.

La question de revue reste concrète : quelle règle cet agrégat protège-t-il, par quels chemins peut-on le changer et quelle preuve couvre les accès concurrents ?

## 18. Exercice : une commande devenue agrégat géant

Une équipe place compte, paiement, bénéficiaire, notifications et historique de tous les échanges dans un agrégat unique. Elle justifie le choix en disant que tout appartient au même parcours. Les notifications sont modifiées directement par un traitement séparé.

Identifier les contradictions et proposer une méthode de révision.

Une réponse argumentée commence par les invariants et les cycles de vie. Elle distingue les éléments qui doivent changer de manière cohérente et ceux qui peuvent être référencés ou coordonnés séparément. Elle vérifie les effets de la séparation sur les exigences plutôt que de réduire arbitrairement la taille.

Elle corrige également le chemin de modification direct qui contourne la racine. Enfin, elle relie la nouvelle frontière aux transactions et aux tests concurrents. Le partage d’un parcours ne suffit pas à imposer un agrégat unique.

## 19. Une structure guidée par les règles

La conception tactique donne une forme précise au modèle : l’entité porte une continuité, l’objet-valeur exprime une caractéristique et l’agrégat protège une cohérence. Les services, factories et repositories complètent cette organisation lorsque les responsabilités le demandent.

Le résultat ne se juge pas au nombre de patterns utilisés. Il se juge à la clarté des comportements, à la préservation des invariants et à la correspondance entre les règles et leur réalisation.

Le chapitre suivant examinera le monolithe modulaire : comment assembler ces responsabilités dans une application livrée ensemble sans perdre les frontières et la possibilité d’évolution ?

## Références

[1] Eric Evans, *Domain-Driven Design Reference — Definitions and Pattern Summaries*, Domain Language, 2015 ; entités, objets-valeur, services, agrégats, événements, repositories et factories : https://www.domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf

[2] Microsoft Learn, *Use tactical DDD to design microservices*, modèles internes, agrégats, identités, services et événements ; les principes sont étudiés ici sans imposer un déploiement en microservices : https://learn.microsoft.com/en-us/azure/architecture/microservices/model/tactical-domain-driven-design

[3] Microsoft Learn, *Design validations in the domain model layer*, invariants, construction valide et mutations partielles : https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/domain-model-layer-validations

[4] Microsoft Learn, *Designing a microservice domain model*, frontière d’agrégat et contrôle des changements par la racine : https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/microservice-domain-model

[5] Microsoft Learn, *Handling concurrency conflicts*, exemple de jeton et d’écriture conditionnelle en concurrence optimiste ; la source décrit EF Core, non une implémentation Java fournie par ce chapitre : https://learn.microsoft.com/en-us/ef/core/saving/concurrency

[Chapitre précédent](chapitre-02.md) · [Sommaire](../SOMMAIRE.md)
