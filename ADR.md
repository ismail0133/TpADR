# ADR-001 — Architecture modulaire avec séparation des domaines métier

## Statut
Accepté

## Date
2026-05-05

## Contexte
ProxiVroum est une plateforme régionale de covoiturage avec plusieurs acteurs : Conducteur, Passager, Modérateur et Service Comptabilité.

La plateforme doit gérer plusieurs fonctionnalités : publication d’un trajet, recherche, réservation, paiement, notation après trajet, support en cas de litige, statistiques modérateur et exports comptables.

Le système doit aussi respecter des contraintes importantes :
- pics de charge le vendredi soir et le dimanche soir ;
- disponibilité visée de 99,5 % ;
- paiement conforme PCI-DSS ;
- protection des données personnelles selon le RGPD.

Ces contraintes imposent une architecture claire, maintenable et capable d’évoluer sans créer un bloc monolithique difficile à modifier.

## Décision
Nous choisissons une architecture modulaire orientée services, avec une séparation claire des domaines métier.

Les principaux modules/services sont :
- **Service Trajets** : publication, modification, suppression et consultation des trajets ;
- **Service Recherche & Réservation** : recherche de trajets disponibles et création des réservations ;
- **Service Paiement** : gestion des paiements via un prestataire conforme PCI-DSS ;
- **Service Avis & Notation** : notation après trajet ;
- **Service Modération** : gestion des litiges, signalements et statistiques ;
- **Service Comptabilité** : génération des exports comptables.

Le paiement sera délégué à un prestataire externe certifié PCI-DSS afin d’éviter de stocker directement les données bancaires sensibles dans ProxiVroum.

## Conséquences positives
- Meilleure maintenabilité grâce à la séparation des responsabilités.
- Possibilité de faire évoluer ou scaler certains modules indépendamment, notamment la recherche et la réservation pendant les pics de charge.
- Réduction des risques liés au paiement grâce à l’utilisation d’un prestataire conforme PCI-DSS.
- Meilleure conformité RGPD grâce à une séparation plus claire des données personnelles et des accès.
- Facilite le travail en équipe car chaque membre peut travailler sur un domaine précis.

## Conséquences négatives
- Architecture plus complexe qu’une application monolithique simple.
- Besoin de bien gérer la communication entre les services.
- Nécessite une supervision technique plus sérieuse : logs, monitoring, alertes et suivi des erreurs.
- Les tests d’intégration deviennent plus importants.

## Alternatives étudiées

### Alternative 1 — Architecture monolithique
Une seule application regroupe toutes les fonctionnalités.

**Avantages :**
- Plus simple à développer au départ.
- Moins de complexité technique.
- Déploiement initial plus rapide.

**Inconvénients :**
- Difficile à maintenir lorsque le projet grandit.
- Moins adapté aux pics de charge ciblés.
- Risque de couplage fort entre les fonctionnalités.

### Alternative 2 — Microservices complets dès le départ
Chaque fonctionnalité est développée comme un microservice indépendant.

**Avantages :**
- Très scalable.
- Très flexible.
- Chaque service peut évoluer indépendamment.

**Inconvénients :**
- Complexité élevée pour un projet académique.
- Besoin de gestion avancée : API Gateway, discovery, monitoring, CI/CD, sécurité interservices.
- Risque de perdre du temps sur l’infrastructure plutôt que sur la valeur métier.

## Justification
L’architecture modulaire orientée services représente un bon compromis : elle reste compréhensible et réalisable dans le cadre du projet, tout en répondant aux contraintes de disponibilité, de charge, de sécurité et de conformité.

Elle permet de construire progressivement ProxiVroum sans tomber dans une complexité excessive dès le début.

## Décision finale
Nous retenons une architecture modulaire avec séparation des domaines métier, et une intégration externe pour le paiement conforme PCI-DSS.
