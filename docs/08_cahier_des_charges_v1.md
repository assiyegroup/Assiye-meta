## Cahier des charges — Assiye (V1)

### 1) Contexte & problématique
Au Togo, les habitants ont du mal à trouver rapidement des produits/services de proximité (disponibilité, horaires, localisation). Les commerçants (fixes et ambulants) manquent de visibilité et d’outils simples pour toucher leur clientèle.

### 2) Objectif général
Développer une plateforme **mobile** de mise en relation entre commerçants locaux et habitants, complétée par un **dashboard web** d’administration, en centralisant : localisation, horaires, annonces, promotions et contacts.

### 3) Objectifs spécifiques (V1)
- Publier des **annonces** (produits/services) par les vendeurs.
- Permettre la **recherche** et la découverte (filtres, proximité).
- Faciliter la **mise en relation** (WhatsApp / appel / email).
- Offrir une **carte** et l’accès aux infos utiles (point vendeur + horaires).
- Mettre en place une **modération progressive** (signalements, masquage, suspension).
- Assurer une **gestion admin** (catégories, villes/régions, supervision).
- Ajouter des **notifications push (Firebase/FCM)** pour informer rapidement les utilisateurs d’événements importants.
- Mettre en place un système d’**abonnement** : les acheteurs peuvent **suivre des vendeurs** pour recevoir des nouveautés.

### 4) Périmètre V1 (MVP)
#### 4.1 Application mobile (Flutter)
- Authentification (module `akoeleapirest` adapté) : email+mdp + OTP, JWT.
- Parcours Acheteur : recherche, consultation annonce, contact, signalement.
- Parcours Vendeur : **activer le mode vendeur**, profil, point principal + horaires, CRUD annonces, promotions simples.
- Carte : affichage points/annonces et proximité.

#### 4.2 Dashboard admin (Next.js)
- Gestion catégories (CRUD), villes/régions (CRUD).
- Supervision annonces & promotions (liste, filtres, actions).
- Gestion signalements (traitement, statut, actions).
- Gestion comptes vendeurs (avertir/suspendre).

#### 4.3 API (NestJS)
- Auth + RBAC.
- Données de base : catégories, régions/villes.
- Annonces, médias, promotions.
- Signalements + actions admin (audit).

### 5) Hors-scope V1
- Paiements/commandes/livraison.
- Messagerie interne (prévue V2).
- Système de notation/avis (V2).
- Tracking avancé et recommandations (V3).

### 6) Acteurs & rôles
- **Utilisateur (compte unique)** : consulter/rechercher/contacter/signaler/suivre.
- **Mode vendeur (capacité)** : gérer profil vendeur, point principal + horaires, annonces, promos.
- **Admin secondaire** : gérer référentiels (catégories, villes/régions), supervision.
- **Admin principal** : supervision globale, gestion admins, modération lourde.

> V1 : compte unique + activation du mode vendeur (création/activation d’un `seller_profile`).

### 7) Exigences fonctionnelles (V1)
#### 7.1 Référentiels
- Catégories : créer, modifier, supprimer, activer/désactiver.
- Régions/Villes : CRUD, association ville→région.

#### 7.2 Vendeur — profil + localisation
- Profil vendeur : nom, activité, contacts (WhatsApp obligatoire en V1).
- **Localisation V1** : **1 point principal** (lat/lng + libellé + adresse) + **horaires**.
- Filtre “ouvert maintenant” basé sur horaires.

#### 7.3 Annonces
- CRUD annonces (titre, description, catégorie, prix ou “sur demande”, photos).
- Statuts : publié/masqué.
- Promotions simples liées à une annonce (période + label).

#### 7.4 Recherche & découverte
- Recherche texte.
- Filtres : catégorie, distance (ou ville), promotions.
- Tri : plus récent / plus proche.

#### 7.5 Mise en relation
- Lien WhatsApp direct (pré-rempli si possible).
- Appel et email (si disponibles).

#### 7.6 Modération & signalements
- Signalement annonce par acheteur (motif + commentaire optionnel).
- Traitement admin : masquer annonce / avertir / suspendre vendeur.
- Journalisation des actions admin (audit).

#### 7.7 Notifications (Firebase Cloud Messaging)
- L’application mobile peut **enregistrer** un token FCM.
- Le backend peut envoyer des **push notifications** via FCM.
- Les notifications importantes sont consultables (liste + non lues) et peuvent être marquées “lues”.

**Cas V1 recommandés**
- Vendeur : compte averti/suspendu, annonce masquée, annonce signalée.
- Acheteur : (optionnel V1) confirmation signalement.
 - Acheteur (abonné) : nouvelle annonce d’un vendeur suivi.

#### 7.8 Abonnements (Acheteur ↔ Vendeur)
- Un acheteur peut **s’abonner** à un vendeur.
- Un acheteur peut **se désabonner**.
- Un vendeur peut voir (optionnel V1) son nombre d’abonnés.
- Lorsqu’un vendeur publie une annonce, les abonnés peuvent recevoir une **notification push**.

### 8) Exigences non fonctionnelles
- **Performance** : recherche et liste d’annonces réactive (pagination).
- **Sécurité** : validation stricte des DTO, rate limit sur auth/signalements, RBAC.
- **Disponibilité** : logs + monitoring basiques (V1).
- **Qualité** : conventions de code, linting, CI minimale (optionnel V1).
- **Confidentialité** : minimiser l’expo des données; pas de tracking sensible en V1.

### 9) Contraintes & hypothèses
- V1 en **4 semaines minimum**.
- Pas de paiement.
- Carte : Google Maps ou OpenStreetMap (données agnostiques : `lat/lng`).
- Auth : réutilisation du module `akoeleapirest` (OTP email + JWT + refresh).
- Médias : **Supabase Storage** (bucket) via l’API.
- Notifications : **Firebase Cloud Messaging (FCM)** (comme `akoeleapirest`).

### 10) Risques & mitigations
- **Abus/spam** : limites (rate limit), quotas annonces, modération progressive.
- **Coût/quotas cartes** : option OpenStreetMap; limiter appels geocoding.
- **Cookies refresh sur mobile** : ajouter un mode refresh via body/header.

### 11) Planning indicatif (4 semaines)
- **S1** : cadrage + data model + setup API + auth + référentiels.
- **S2** : annonces + médias + recherche (API) + écrans Flutter principaux.
- **S3** : carte + horaires + promotions + signalements + dashboard admin.
- **S4** : durcissement sécurité, QA, documentation, préparation déploiement.


