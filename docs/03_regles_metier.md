## Règles métier (V1)

### Rôles & permissions (RBAC)
- **Utilisateur (compte unique)** : consulter, rechercher, contacter, signaler, suivre des vendeurs.
- **Mode vendeur (capacité)** : disponible si `seller_profile` est activé → gérer profil vendeur, point principal + horaires, annonces, promotions.
- **Admin secondaire** : gérer catégories, villes/régions; superviser annonces/promotions; traiter signalements.
- **Admin principal** : tout + gestion des admins + support + décisions de modération lourdes.

> On n’impose pas “Acheteur vs Vendeur” comme comptes distincts : un utilisateur peut activer le **mode vendeur** (bascule) sans recréer de compte.

### Comptes & statuts
#### Statut vendeur
- `active` : peut publier/éditer.
- `warned` : avertissement (limites possibles).
- `suspended` : ne peut plus publier ni être visible (annonces masquées).

#### Statut annonce
- `draft` : non visible (optionnel, si on veut).
- `published` : visible.
- `hidden` : masquée par modération (ou par le vendeur).

### Publication d’annonces
- Une annonce doit avoir (V1) : **titre, catégorie, description, prix (ou “sur demande”), 1+ photo, point vendeur + horaires, contacts**.
- Un vendeur ne peut pas publier si son compte est `suspended`.
- Option V1 : limiter le nombre d’annonces par vendeur (ex: 20) pour éviter le spam au démarrage.

### Activer le mode vendeur (V1)
- Un utilisateur peut activer le mode vendeur après avoir :
  - complété son profil vendeur (contacts)
  - défini son point principal + horaires
- À l’activation, on crée (ou active) un `seller_profile`.
- Tant que le `seller_profile.status != active`, les endpoints vendeur sont bloqués.

### Promotions
- Une promo est liée à une annonce.
- Champs recommandés : `type` (pourcentage / montant / “offre spéciale”), `start_at`, `end_at`, `label`.
- Une promo expirée n’est plus affichée.

### Signalement & modération progressive
#### Signalement
- Un acheteur peut signaler une annonce avec :
  - motif (spam, arnaque, contenu interdit, doublon, autre)
  - commentaire optionnel
- Limite anti-abus : max X signalements/jour/utilisateur.

#### Actions admin (V1)
- Masquer une annonce (`hidden`) + noter la raison.
- Avertir le vendeur (`warned`) + message.
- Suspendre le vendeur (`suspended`) après seuil ou cas grave.

#### Règles automatiques (simples, V1)
- Si une annonce reçoit >= N signalements uniques en 24–48h → passer en **“à revoir”** (liste prioritaire admin) ou masquer automatiquement selon la tolérance.
- Si un vendeur reçoit >= M annonces masquées → avertissement puis suspension.

### Contacts & traçabilité
- Chaque clic “WhatsApp / Appel / Email” peut être loggé (anonyme ou user_id) pour statistiques simples (V2).
- Les actions admin doivent être auditées (qui a fait quoi, quand, pourquoi).

### Abonnements (follow) & notifications aux abonnés
- Un utilisateur **acheteur** peut suivre un vendeur (`follow`) et se désabonner (`unfollow`).
- Un acheteur ne peut pas suivre le même vendeur deux fois (unicité).
- **Anti-spam V1** (recommandé) :
  - limiter le nombre de notifications “nouvelle annonce” envoyées par vendeur sur une période (ex: max 3/jour).
  - permettre à l’utilisateur de désactiver les notifications liées aux abonnements (V1.1 si nécessaire).


