# Chapitre 5 — Les architectures en couches et N-tier

## 1. Quatre dossiers ne font pas quatre serveurs

Une application possède un contrôleur, un service applicatif, un modèle métier et un repository. Faut-il déployer chacun séparément ? La question paraît élémentaire, mais elle révèle une confusion fréquente entre organisation du code et distribution de l’exécution.

Une couche, ou layer, représente une séparation logique de responsabilités. Un tier représente une séparation physique de déploiement. Plusieurs couches peuvent fonctionner dans le même processus ; la distribution de ces couches dans plusieurs processus ou serveurs introduit d’autres contraintes. Microsoft distingue explicitement ces deux dimensions. [1][2]

Le vocabulaire varie selon les documents. Dans ce livre, nous utiliserons « couche » pour le découpage logique et « niveau de déploiement » ou « tier » pour la répartition physique. Nous préciserons aussi ce que nous comptons : navigateur, frontal, serveur applicatif ou stockage ne sont pas toujours inclus de la même manière dans les appellations commerciales.

Le monolithe modulaire du chapitre précédent peut donc contenir plusieurs couches. Le présent chapitre explique leur rôle, leurs dépendances et les conséquences d’une distribution éventuelle.

## 2. Séparer présentation, orchestration, métier et accès aux données

L’organisation traditionnelle à trois couches distingue la présentation, la logique métier et l’accès aux données. Les noms UI, BLL et DAL désignent souvent ces responsabilités. Microsoft décrit cette structure et ses dépendances descendantes. [2]

```text
Présentation — interface et adaptation des échanges
        ↓
Logique métier — traitements et décisions
        ↓
Accès aux données — réalisation de la persistance
```

La couche métier peut ensuite être affinée en orchestration applicative et domaine. Cette séparation aide à distinguer le déroulement d’un cas d’usage des règles qu’il doit respecter.

| Responsabilité | Exemple dans notre application |
|---|---|
| Présentation | Lire la demande HTTP et traduire la réponse |
| Application | Charger le paiement, coordonner la confirmation et persister |
| Domaine | Vérifier si la confirmation est légitime dans le modèle |
| Accès aux données | Lire et écrire les représentations persistées |

Cette grille n’impose pas quatre couches en cascade. Dans notre variante orientée domaine, l’application utilise le domaine et un contrat de persistance. Les dépendances exactes seront décrites, plutôt que déduites du nombre de noms dans le tableau.

La distinction est utile lorsqu’une règle est appelée depuis plusieurs entrées. Une API, un traitement batch et une interface d’assistance ne doivent pas inventer chacun leur propre interprétation d’une transition.

## 3. Définir les dépendances autorisées

Une architecture en couches ne consiste pas seulement à regrouper les classes. Elle définit quelles responsabilités peuvent en utiliser d’autres.

Dans un modèle fermé, une couche appelle uniquement celle immédiatement inférieure. Dans un modèle ouvert, elle peut appeler des couches inférieures non adjacentes. Microsoft présente ces deux variantes et leurs compromis. [1]

```text
Variante fermée :
Présentation → Application → Traitements → Accès aux données

Variante ouverte :
Présentation → Application → Traitements
Application ─────────────────→ Accès aux données
```

Ces exemples montrent des conventions de dépendance, pas un modèle obligatoire du domaine. Le sens de chaque couche doit être suffisamment précis pour expliquer ce qui est autorisé.

Le modèle fermé limite les contournements, mais peut introduire des couches qui transmettent simplement les appels. Le modèle ouvert peut simplifier certains usages tout en multipliant les dépendances. Si les couches sont locales, le coût n’est pas un saut réseau ; ce coût apparaît lorsqu’une séparation physique transforme la traversée en communication distante.

Une exception de lecture peut être choisie, mais elle doit rester explicite. Autoriser partout des accès directs à la persistance finit par rendre les couches annoncées sans effet.

## 4. Le parcours d’une confirmation

Reprenons le modèle Paiement introduit au chapitre 3. Le domaine sait contrôler une transition, mais ne sait pas recevoir une requête HTTP ni décider comment ouvrir une transaction de stockage.

Le contrôleur adapte une entrée au cas d’usage. L’application vérifie les conditions d’accès selon la conception retenue, charge l’agrégat, lui demande d’appliquer la règle et coordonne l’écriture. La réalisation du repository effectue les accès au stockage.

```text
Entrée HTTP
    ↓ adaptation et contrôles d’entrée
Cas d’usage ConfirmerPaiement
    ├──→ Repository : charger
    ├──→ Paiement : appliquer la règle
    └──→ Repository : sauvegarder
    ↓ traduction du résultat
Réponse HTTP
```

Une réponse du partenaire doit avoir été interprétée selon son contrat avant d’être présentée comme une confirmation reconnue. La couche de présentation ne doit pas convertir toute réussite technique en succès métier.

La même opération peut être appelée depuis un autre adaptateur. Le domaine et l’orchestration ne devraient pas dépendre du fait que l’entrée provient d’un navigateur ou d’un traitement de messages. Cette propriété doit être vérifiée dans les dépendances du code.

Le chemin de consultation peut être différent du chemin de modification. Il peut produire une représentation de lecture sans charger un agrégat complet, à condition de préserver les règles d’accès et le sens des informations exposées.

## 5. Placer la transaction au niveau du cas d’usage

Une sauvegarde isolée peut réussir sans que le cas d’usage complet soit cohérent. Si plusieurs écritures doivent être validées ensemble dans un même stockage transactionnel, leur frontière doit être organisée au niveau approprié.

Le fragment suivant montre une orchestration Spring utilisant l’agrégat du chapitre 3. Il est illustratif, non compilé ni exécuté ici. Le repository est un contrat simplifié, pas une implémentation de persistance.

```java
import java.util.Optional;
import java.util.UUID;
import org.springframework.transaction.annotation.Transactional;

public class ConfirmationPaiementService {
    private final PaiementRepository paiements;

    public ConfirmationPaiementService(
            PaiementRepository paiements) {
        this.paiements = paiements;
    }

    @Transactional
    public boolean confirmer(UUID paiementId, String reference) {
        Paiement paiement = paiements.trouver(paiementId)
            .orElseThrow(() -> new IllegalArgumentException(
                "Paiement introuvable"));

        boolean changement = paiement.confirmer(reference);
        if (changement) {
            paiements.sauvegarder(paiement);
        }
        return changement;
    }

    public interface PaiementRepository {
        Optional<Paiement> trouver(UUID id);
        void sauvegarder(Paiement paiement);
    }
}
```

Le code suppose que l’objet est géré comme un bean et que l’infrastructure transactionnelle appropriée est activée. Spring précise que la présence de l’annotation seule ne suffit pas à produire ce comportement. [3]

Il faut également relier le contrat aux garanties de concurrence du stockage. L’annotation ne remplace pas la stratégie de version étudiée au chapitre 3.

Le choix des exceptions compte. Les règles de rollback dépendent des paramètres et de la configuration retenus. Il ne faut pas supposer que toute exception déclenche nécessairement la même annulation. La documentation Spring décrit ces règles et leurs possibilités de personnalisation. [3]

Enfin, en mode proxy, un appel interne à la même instance n’est pas intercepté comme un appel passant par le proxy. Une méthode annotée appelée par une autre méthode du même objet ne reçoit donc pas automatiquement une nouvelle frontière transactionnelle. Elle peut toujours s’exécuter dans une transaction déjà existante. [3]

## 6. Ce que la séparation physique ajoute

Imaginons maintenant que la présentation et le serveur applicatif soient des processus distincts. La méthode de cas d’usage devient accessible au travers d’un contrat distant. Sa structure interne ne suffit plus à décrire l’interaction.

Il faut préciser sérialisation, authentification, autorisation, délai, erreurs et compatibilité. Une panne peut affecter un niveau alors que les autres fonctionnent. La durée totale comprend les attentes et les communications supplémentaires.

Microsoft présente cette séparation comme un moyen de différencier les besoins de capacité, de résilience et de sécurité, avec un coût de communication et d’exploitation. [1]

| Organisation | Ce qu’elle change |
|---|---|
| Plusieurs couches dans un processus | Dépendances logiques et appels locaux |
| Présentation et application séparées | Contrat distant et défaillances partielles |
| Stockage sur un service distinct | Connexions, transactions et dépendance de disponibilité |
| Plusieurs instances d’un tier | Répartition de charge et état partagé à examiner |

Le dernier cas ne crée pas une nouvelle couche. Il multiplie les instances d’une responsabilité de déploiement.

Il faut éviter une distribution trop fine des appels. Une interface distante permettant de récupérer séparément chaque champ d’un paiement multiplie les échanges et peut assembler des observations prises à des instants différents. Un contrat adapté au cas d’usage est souvent plus cohérent qu’une exposition distante de toutes les méthodes internes.

## 7. Comprendre un déploiement trois tiers

Considérons trois blocs de déploiement côté serveur : un frontal web, un serveur applicatif et un service de données. Le navigateur est ici représenté à l’extérieur de ces trois blocs ; cette convention évite l’ambiguïté du comptage.

```text
Navigateur ou application cliente
              │
              ▼
Tier web : exposition et routage
              │
              ▼
Tier applicatif : cas d’usage et règles
              │
              ▼
Tier de données : stockage persistant
```

Dans un patrimoine Java, le frontal peut être un composant HTTP ou un proxy, le tier applicatif peut héberger une application sur un runtime adapté et le stockage peut être relationnel. Ces éléments décrivent une possibilité de réalisation, pas une exigence propre à Java.

Le frontal n’est pas nécessairement la totalité de la présentation. Une partie de l’interface peut s’exécuter dans le navigateur ; des contrôleurs peuvent résider dans le serveur applicatif. Les responsabilités logiques doivent donc être reliées aux processus réels au lieu de reprendre les étiquettes sans examen.

Chaque tier peut comporter plusieurs instances. Les flux, les droits réseau, les accès administratifs et les points communs de défaillance doivent être examinés séparément. Une séparation en sous-réseaux peut limiter l’exposition sans démontrer l’autorisation métier d’un utilisateur.

## 8. Articuler modules métier et couches

Le monolithe modulaire et l’architecture en couches ne sont pas deux alternatives incompatibles. Les modules séparent principalement des responsabilités fonctionnelles ; les couches séparent des préoccupations à l’intérieur de leur réalisation.

```text
Application
├── Paiements
│   ├── Présentation
│   ├── Application
│   ├── Domaine
│   └── Accès aux données
└── Résolution
    ├── Présentation
    ├── Application
    ├── Domaine, si les règles le justifient
    └── Accès aux données
```

Cette organisation reprend la frontière par module du chapitre précédent. Elle évite qu’un contrôleur de Résolution puisse utiliser directement un repository interne à Paiements sous prétexte qu’ils appartiennent à des couches compatibles.

Il faut donc combiner les deux règles : respecter les dépendances internes aux couches et les contrats entre modules. Une vérification ne remplace pas l’autre.

Un module simple peut fusionner certaines responsabilités. Ajouter des couches sans comportement distinct peut rendre les changements plus difficiles sans mieux protéger le métier.

La structure doit rester lisible pour les personnes qui modifient les cas d’usage. Elle ne doit pas imposer de traverser une succession de classes ne faisant que recopier les mêmes paramètres.

## 9. Reconnaître les fuites de responsabilité

La première fuite apparaît lorsque le contrôleur contient les règles de transition. Un traitement batch doit alors les recopier ou les contourne.

La deuxième apparaît lorsque le domaine dépend directement d’un format de stockage ou d’une classe de framework dont il n’a pas besoin pour exprimer ses décisions. Le changement technique se propage alors au modèle. Microsoft décrit cette limite des dépendances traditionnelles descendantes. [2]

La troisième concerne les données exposées. Retourner directement une entité persistée peut rendre le contrat externe dépendant de ses associations, de ses champs internes et des conditions de chargement. Un modèle de réponse explicite permet de choisir ce qui est publié.

La quatrième est la fuite d’erreur. Une exception SQL ou un détail du mapping ne constitue pas un résultat métier intelligible. Il faut distinguer rejet de règle, absence autorisée, conflit concurrent et indisponibilité.

Ces problèmes ne se corrigent pas simplement en déplaçant une classe dans un autre dossier. Il faut examiner les dépendances et les responsabilités effectivement exercées.

## 10. Choisir les couches utiles et vérifier leurs effets

Une architecture en couches est particulièrement lisible lorsque les responsabilités sont stables et que les conventions sont comprises. Elle permet d’isoler des éléments pour les tests et de limiter certaines conséquences du changement. [2]

Elle devient moins utile lorsque chaque couche n’est qu’un relais, lorsque les interfaces diffusent toutes les structures internes ou lorsque le découpage physique introduit des appels sans bénéfice identifié. Microsoft souligne notamment le coût d’un tier intermédiaire qui n’apporte qu’un CRUD élémentaire. [1]

Pour notre cas fictif, les preuves à construire sont les suivantes :

| Situation | Propriété à vérifier |
|---|---|
| Confirmation depuis HTTP ou batch | Règle unique et cohérente |
| Échec après une première écriture | Comportement transactionnel réel |
| Accès à un repository d’un autre module | Dépendance interdite détectée |
| Indisponibilité d’un tier distant | Erreur comprise et traitement défini |
| Montée en charge de la consultation | Temps de bout en bout et saturation observés |

Les tests unitaires du domaine ne valident pas la configuration transactionnelle. Un test d’intégration doit passer par les mécanismes réellement utilisés, notamment le proxy lorsque cette stratégie est retenue.

De même, un essai local ne prouve pas les performances d’un déploiement distribué. Les points de mesure doivent couvrir les communications et les conditions représentatives.

## 11. Exercice : cinq couches pour une seule requête

Une équipe propose de séparer sur cinq services distants le contrôleur, la validation, la règle, le repository et le mapping SQL. Elle affirme que cinq couches apportent automatiquement davantage de modularité et de disponibilité.

Identifier les décisions confondues. Proposer une organisation plus simple, puis les conditions qui justifieraient certaines séparations physiques.

Une réponse argumentée commence par distinguer responsabilités logiques et processus. Elle peut conserver plusieurs couches dans un serveur applicatif, avec un contrat distant seulement aux frontières pertinentes. Elle examine ensuite les exigences d’exposition, de capacité et d’exploitation.

La validation de format peut être réalisée à l’entrée, la règle métier dans le domaine et la persistance par un adaptateur. Le stockage peut rester un service distinct sans que chaque classe d’accès aux données devienne un service réseau.

Si une séparation physique est retenue, la correction doit expliquer son bénéfice, son contrat et son coût. Multiplier les instances et multiplier les niveaux ne sont pas des opérations équivalentes.

## 12. De la séparation des responsabilités à l’orientation des dépendances

Une architecture en couches donne une structure aux responsabilités et aux relations autorisées. Une architecture N-tier ajoute une répartition physique dont les bénéfices et les coûts doivent être établis.

Le nombre de couches n’indique ni la qualité du modèle ni la résilience du service. Ce sont les responsabilités, les contrats et les mécanismes réalisés qui permettent de vérifier les propriétés attendues.

Une dernière question reste ouverte : faut-il que le métier dépende de la réalisation de l’accès aux données ? La séparation logique ne résout pas nécessairement ce problème. Le chapitre suivant examinera l’inversion des dépendances et les approches hexagonale, Clean et Onion, afin de protéger le cœur applicatif sans confondre leurs conventions.

## Références

[1] Microsoft Learn, *N-tier architecture style*, couches logiques, tiers physiques, variantes ouvertes et fermées, coûts et choix de déploiement : https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/n-tier

[2] Microsoft Learn, *Common web application architectures*, couches traditionnelles, monolithe, responsabilités et orientation des dépendances ; exemples d’origine .NET, principes discutés ici pour une conception Java : https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures

[3] Spring Framework, *Using @Transactional*, activation, mode proxy, auto-invocation et règles transactionnelles ; comportements à vérifier selon la version et la configuration retenues : https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html

[Chapitre précédent](chapitre-04.md) · [Sommaire](../SOMMAIRE.md)
