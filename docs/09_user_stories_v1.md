## User stories V1 (détaillées) — avec critères d’acceptation

### Légende
- Priorité : P0 (indispensable), P1 (important), P2 (plus tard)

## Epic A — Auth & comptes

### US-A1 (P0) — Inscription
En tant qu’utilisateur, je peux m’inscrire avec email + mot de passe afin d’accéder à l’application.

**Critères d’acceptation**
- L’email est valide et unique.
- Le mot de passe respecte la politique (min longueur).
- Le système envoie un OTP (email) après l’inscription.

### US-A2 (P0) — Vérification OTP
En tant qu’utilisateur, je peux saisir l’OTP reçu afin de valider mon compte et obtenir un JWT.

**Critères d’acceptation**
- OTP expiré → erreur claire.
- OTP invalide → erreur claire.
- Succès → retour d’un access token (JWT).

### US-A3 (P0) — Connexion + OTP
En tant qu’utilisateur, je peux me connecter (email+mdp), recevoir un OTP puis valider l’OTP pour obtenir un JWT.

**Critères d’acceptation**
- Mauvais mdp → erreur.
- OTP envoyé après mdp correct.
- Vérification OTP retourne un JWT.

## Epic B — Référentiels

### US-B1 (P0) — Gestion catégories (Admin)
En tant qu’admin, je peux créer/modifier/supprimer des catégories pour structurer les annonces.

**Critères d’acceptation**
- CRUD complet.
- Catégorie inactive non proposée à la création d’annonce.

### US-B2 (P0) — Gestion villes/régions (Admin)
En tant qu’admin, je peux gérer régions et villes pour organiser les annonces.

**Critères d’acceptation**
- CRUD complet.
- Ville liée à une région.

## Epic C — Vendeur : profil & point principal

### US-C0 (P0) — Activer le mode vendeur
En tant qu’utilisateur, je peux activer le mode vendeur afin de publier des annonces.

**Critères d’acceptation**
- Si le profil vendeur n’existe pas, il est créé.
- Le mode vendeur n’est actif que si les informations minimales sont complètes (contacts + point + horaires).
- En cas de statut `suspended`, l’activation/publishing est bloqué.

### US-C1 (P0) — Profil vendeur
En tant que vendeur, je peux compléter mon profil (contacts) afin que les acheteurs puissent me joindre.

**Critères d’acceptation**
- WhatsApp (numéro) requis en V1.
- Les informations sont éditables.

### US-C2 (P0) — Définir point principal + horaires
En tant que vendeur, je peux définir un point principal et mes horaires afin d’être trouvé facilement.

**Critères d’acceptation**
- Un seul point principal en V1.
- Horaires : jours + plages (start/end).
- Les acheteurs peuvent voir “ouvert maintenant” si la plage couvre l’heure locale.

## Epic D — Annonces

### US-D1 (P0) — Créer une annonce
En tant que vendeur, je peux créer une annonce (produit/service) afin d’être visible.

**Critères d’acceptation**
- Champs requis : titre, catégorie, description, prix ou “sur demande”, au moins 1 photo.
- Si vendeur suspendu → création refusée.

### US-D2 (P0) — Modifier/supprimer une annonce
En tant que vendeur, je peux mettre à jour ou supprimer mes annonces.

**Critères d’acceptation**
- Un vendeur ne modifie que ses annonces.
- Suppression = soft delete ou suppression logique (à décider).

### US-D3 (P1) — Promotions simples
En tant que vendeur, je peux ajouter une promotion (période + label) sur une annonce.

**Critères d’acceptation**
- Promo expirée n’apparaît plus.
- Une promo est liée à une annonce.

## Epic E — Acheteur : recherche & consultation

### US-E1 (P0) — Recherche + filtres
En tant qu’acheteur, je peux rechercher des annonces par mot-clé et filtrer pour trouver rapidement.

**Critères d’acceptation**
- Filtre par catégorie.
- Filtre par distance (si localisation autorisée) ou ville.
- Tri : plus récent / plus proche.

### US-E2 (P0) — Voir détail annonce
En tant qu’acheteur, je peux consulter le détail d’une annonce pour décider de contacter le vendeur.

**Critères d’acceptation**
- Afficher photos, description, prix, promo (si active).
- Afficher point vendeur + horaires.

## Epic F — Mise en relation

### US-F1 (P0) — Contacter via WhatsApp
En tant qu’acheteur, je peux contacter le vendeur via WhatsApp.

**Critères d’acceptation**
- Lien WhatsApp fonctionne depuis mobile.
- Le numéro WhatsApp du vendeur est affiché/accessible.

### US-F2 (P1) — Contacter via appel/email
En tant qu’acheteur, je peux appeler ou envoyer un email au vendeur.

**Critères d’acceptation**
- Boutons actifs seulement si les infos existent.

## Epic G — Modération progressive

### US-G1 (P0) — Signaler une annonce
En tant qu’acheteur, je peux signaler une annonce afin d’aider à garder la plateforme propre.

**Critères d’acceptation**
- Motif requis.
- Anti-abus : limite X signalements/jour.

### US-G2 (P0) — Traitement signalements (Admin)
En tant qu’admin, je peux traiter les signalements afin d’agir rapidement.

**Critères d’acceptation**
- Liste des signalements (filtre par statut).
- Action : masquer annonce / avertir / suspendre vendeur.
- Trace dans l’audit.

## Epic H — Notifications (Firebase/FCM)

### US-H1 (P0) — Enregistrer un token FCM
En tant qu’utilisateur, je peux enregistrer mon token Firebase afin de recevoir des notifications push.

**Critères d’acceptation**
- L’utilisateur connecté peut enregistrer un token (android/ios).
- Un même token est unique en base.
- L’utilisateur peut désinscrire un token.

### US-H2 (P1) — Recevoir une notification push sur événement important
En tant que vendeur, je reçois une notification push quand une action importante me concerne.

**Critères d’acceptation**
- Lors d’une action admin (avertissement/suspension/annonce masquée), une notification est créée et envoyée.
- En cas d’échec d’envoi, la notification conserve un statut `failed`.

### US-H3 (P1) — Consulter et marquer comme lues mes notifications
En tant qu’utilisateur, je peux voir mes notifications et les marquer comme lues.

**Critères d’acceptation**
- Liste paginée.
- Compteur de non lues.
- Marquage “lu” (par id et “tout lire”).

## Epic I — Abonnements vendeurs (follow)

### US-I1 (P0) — Suivre un vendeur
En tant qu’acheteur, je peux suivre un vendeur afin d’être informé de ses nouveautés.

**Critères d’acceptation**
- Un acheteur connecté peut suivre un vendeur.
- Suivi unique (pas de doublon).

### US-I2 (P0) — Se désabonner
En tant qu’acheteur, je peux me désabonner d’un vendeur.

**Critères d’acceptation**
- Après désabonnement, aucune notif liée à ce vendeur n’est reçue.

### US-I3 (P1) — Notifications aux abonnés (nouvelle annonce)
En tant qu’acheteur abonné, je reçois une notification push lorsqu’un vendeur suivi publie une annonce.

**Critères d’acceptation**
- À la publication d’une annonce, l’API déclenche l’envoi FCM aux abonnés.
- Mise en place d’une limite anti-spam (ex: plafond par vendeur/jour).


