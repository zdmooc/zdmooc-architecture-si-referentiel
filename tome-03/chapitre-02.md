# Chapitre 2 — Distinguer commandes, requêtes, messages et événements

## 1. Transporter un sens, pas seulement des données

Le chapitre précédent séparait prise en charge et résultat. Il faut maintenant préciser ce que les échanges demandent ou affirment. Une file contient des messages ; elle ne permet pas, seule, de savoir s’il s’agit d’ordres, de questions ou de faits.

Une commande exprime une intention de changement. Une requête demande une information sans demander de mutation métier. Un événement rapporte un fait reconnu. Le message est l’unité transportée et peut porter l’une de ces significations. La distinction entre intention dirigée et événement publié est explicitée par Microsoft. [1]

## 2. Définir la responsabilité du destinataire

Dans notre cas fictif, SoumettreDemande est une commande : le destinataire décide si elle est autorisée et valide. DemandeSoumise est un événement uniquement si le jalon annoncé a réellement été établi. LireResultat est une requête ; elle ne doit pas déclencher discrètement une nouvelle soumission.

| Échange proposé | Attente |
|---|---|
| SoumettreDemande | Décision sur une intention |
| RechercherResultat | Ouverture contrôlée d’une investigation |
| LireResultat | Dernière connaissance disponible |
| ExecutionConfirmee | Information sur un fait reconnu |

Un destinataire logique peut posséder plusieurs instances. À l’inverse, plusieurs types de consommateurs peuvent réagir indépendamment à un événement. Multiplier les destinataires d’une commande sensible ne doit pas multiplier involontairement ses effets.

## 3. Distinguer fait et désir

Un nom au passé ne suffit pas. PaiementEffectue reste trompeur si le producteur sait seulement que le broker a reçu une publication. Le contrat doit préciser l’autorité, les préconditions et le jalon réellement atteint.

Un événement peut entraîner des obligations chez un consommateur. Cela ne transforme pas le fait en commande : chaque réaction doit rester explicitement attribuée. Si l’émetteur attend une opération déterminée pour poursuivre son processus, une commande ou une coordination explicite peut mieux exprimer cette dépendance.

## 4. Construire une enveloppe lisible

Nous proposons de séparer identité du message, référence métier, type, version du contrat, cause et corrélation. La cause relie un résultat à l’échange qui l’a produit ; la corrélation rassemble le parcours.

Une date de publication n’est pas automatiquement la date du fait. Une version de schéma n’est pas la version d’un agrégat. Les valeurs doivent être nommées selon leur fonction.

Ces identifiants facilitent l’explication, mais n’accordent aucun droit. La provenance et les permissions de publication doivent être vérifiées. Le contenu doit éviter la diffusion inutile de données personnelles.

## 5. Faire évoluer sans imposer une livraison simultanée

Un consommateur peut ignorer un champ inconnu si son contrat le permet. Une nouvelle valeur de statut ou une modification du sens peut cependant le casser sans rendre le document invalide.

Les exemples doivent donc couvrir anciennes versions, refus, doublons et valeurs non reconnues. La documentation décrit aussi les garanties d’ordre, de conservation et de répétition ; un schéma seul n’y suffit pas.

## 6. Exercice : l’événement qui donne un ordre

Un message PaiementTermine est émis pour demander au partenaire d’exécuter un paiement. Corriger le contrat.

La correction distingue la commande de soumission, sa réception et l’événement de résultat reconnu. Elle attribue à chaque émetteur l’autorité nécessaire et explique les résultats négatifs ou inconnus. Le chapitre suivant organise la diffusion de ces faits.

## Référence

[1] Microsoft, *Architecture orientée événements dans le cloud avec Azure Event Grid*, distinction entre commande et événement : https://learn.microsoft.com/fr-fr/archive/msdn-magazine/2018/february/azure-event-driven-architecture-in-the-cloud-with-azure-event-grid

[Précédent](chapitre-01.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-03.md)
