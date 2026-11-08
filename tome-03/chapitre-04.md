# Chapitre 4 — Maîtriser transactions, concurrence et cohérence

## 1. Nommer la propriété à protéger

La cohérence peut désigner la validité d’un agrégat, l’alignement d’une projection ou l’accord entre systèmes. Une transaction locale ne protège pas automatiquement ces trois propriétés.

Dans notre étude, deux confirmations concurrentes ne doivent pas écraser silencieusement une contradiction. Une vue peut être temporairement ancienne sans que le fait de référence soit invalide. Une exécution externe peut exister alors que notre application ne la connaît pas encore.

## 2. Ne pas confondre atomicité et isolation

L’atomicité évite de valider seulement une partie des écritures d’une transaction. L’isolation définit les interactions admises entre transactions concurrentes.

PostgreSQL documente qu’en Read Committed deux lectures successives peuvent voir des données différentes. Son Repeatable Read interdit davantage de phénomènes, mais n’élimine pas toutes les anomalies de sérialisation. Serializable peut refuser une transaction, qui doit alors être reprise intégralement. Ces garanties sont propres au moteur et au niveau retenus. [1]

Le programme doit vérifier la réussite du commit avant d’annoncer un résultat. Il ne doit pas répéter des effets externes à chaque reprise d’une transaction refusée.

## 3. Choisir un contrôle adapté

| Mécanisme | Propriété à examiner |
|---|---|
| Contrainte unique | Unicité durable d’une identité |
| Mise à jour conditionnelle par version | Refus d’une écriture fondée sur un état ancien |
| Verrouillage ciblé | Sérialisation d’accès à une ressource |
| Isolation sérialisable | Contrôle des anomalies entre transactions concernées |

Une version sur une ligne ne protège pas une règle portant sur plusieurs lignes indépendantes. Une règle telle qu’un plafond partagé doit être étudiée dans son véritable périmètre.

Un verrou distribué ne suffit pas à prévenir les actions d’un ancien détenteur qui reprend après une pause. Si ce mécanisme est retenu, l’autorité de stockage ou d’exécution doit pouvoir refuser les détenteurs devenus obsolètes selon le protocole conçu.

## 4. Séparer transaction locale et processus distribué

Un appel HTTP exécuté entre BEGIN et COMMIT ne participe pas automatiquement à la transaction du stockage. Si l’appel réussit puis que le commit échoue, l’effet distant peut subsister.

Une transaction distribuée demande des participants et un protocole compatibles ; elle n’est pas implicite. Une saga organise autrement les étapes locales et leurs suites, sans fournir une isolation globale équivalente. [2]

Le premier choix doit rester la frontière de cohérence nécessaire, pas l’ajout systématique de distribution.

## 5. Encadrer la convergence

Une projection différée doit avoir une source, une méthode de rattrapage et une manière de détecter le retard. Dire cohérence éventuelle ne garantit ni progression en présence d’erreurs permanentes ni délai maximal.

Le parcours utilisateur doit accepter la fraîcheur annoncée ou consulter une autorité adaptée avant une décision sensible.

## 6. Exercice : deux retraits sous un plafond

Deux transactions vérifient séparément un plafond commun puis écrivent chacune une ligne. Leur version locale ne détecte aucun conflit.

La correction identifie la règle partagée, propose une coordination transactionnelle adaptée et teste des transactions indépendantes avec ordonnancement contrôlé. Elle ne conclut pas à la sûreté à partir de deux appels successifs.

## Références

[1] PostgreSQL, *Transaction Isolation* : https://www.postgresql.org/docs/current/transaction-iso.html

[2] Microsoft Learn, *Saga design pattern* : https://learn.microsoft.com/en-us/azure/architecture/patterns/saga

[Précédent](chapitre-03.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-05.md)
