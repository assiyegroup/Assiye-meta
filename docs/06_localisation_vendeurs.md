## Localisation des vendeurs (fixes & ambulants) — proposition V1

### Principe retenu (Option C)
Le vendeur définit des **points de présence** (“où me trouver”) avec des **horaires**.

Cela marche pour :
- **commerçants fixes** : 1 point permanent avec horaires.
- **ambulants** : plusieurs points (marché, carrefour, quartier) + horaires (ex: lundi matin, mardi soir).

### Décision V1
- **V1** : **1 point principal + horaires** (le plus simple à gérer et à comprendre).
- **V1.1/V2** : ajout de **plusieurs points** si besoin.

### UX V1 (simple)
- Le vendeur ajoute un point :
  - nom du point (ex: “Marché d’Adidogomé”)
  - adresse/description
  - coordonnées GPS (pin sur la carte ou recherche)
  - rayon optionnel (ex: 200–500m) si on veut éviter un point trop “strict”
  - horaires (jours + plages horaires)
- L’acheteur :
  - voit le point + horaires
  - peut filtrer “**ouvert maintenant**” (selon l’heure locale)

### Modèle de données (côté API) — agnostique carte
On stocke des **coordonnées** et non une “carte Google/OSM”.
- `seller_locations` :
  - `id`
  - `seller_id`
  - `label`
  - `address_text`
  - `lat`, `lng`
  - `radius_m` (optionnel)
  - `is_primary` (optionnel)
  - `created_at`, `updated_at`
- `seller_location_schedules` :
  - `id`
  - `seller_location_id`
  - `day_of_week` (0–6)
  - `start_time` (HH:MM)
  - `end_time` (HH:MM)
  - `timezone` (optionnel, par défaut celle du pays)

### Évolution (V1.1 / V2 si besoin)
- **Override “je suis ici maintenant”** (Option A en complément) :
  - utile si l’ambulant change exceptionnellement de place
  - on garde un TTL (“visible 30 minutes”) + historique minimal
- **Points récurrents + exceptions** :
  - jours fériés, fermé exceptionnellement, etc.

### Choix carte (Google Maps vs OpenStreetMap)
Quel que soit le fournisseur :
- on consomme `lat/lng` + affichage marker/pin
- la recherche d’adresse peut être :
  - Google Places (si Google Maps)
  - Nominatim (si OpenStreetMap)


