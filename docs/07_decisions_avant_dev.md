## Décisions & points à valider avant de coder (V1)

### Décisions déjà prises
- **V1 (4 semaines min)** : focus MVP.
- **Pas de paiement** : mise en relation uniquement.
- **Mobile** : Flutter.
- **Dashboard** : Next.js.
- **API** : NestJS (en s’appuyant sur `akoeleapirest`).
- **Localisation vendeur (V1)** : **1 point principal + horaires**.
- **Auth (V1)** : on **réutilise** le module `akoeleapirest` (email+mdp+OTP + JWT + refresh).

### À valider (bloquants avant dev)
1) **Rôles**
   - Option rapide : conserver `visitor/host/admin` côté DB et les interpréter comme `buyer/seller/admin`.
   - Option propre : renommer dès maintenant en `buyer/seller/admin` (migration DB mais plus clair).

2) **Stockage images**
   - ✅ Décision : **Supabase Storage** (bucket).
   - Bucket : `assiye-media`
   - Upload via API (service role) + stockage `storage_key` (optionnel) + `url`.

3) **Carte**
   - Google Maps (qualité + Places) vs OpenStreetMap (coût 0, Nominatim).

4) **Données obligatoires V1**
   - Champs annonce : prix (optionnel “sur demande”), photos min 1, etc.
   - Champs vendeur : phone (WhatsApp), email, nom boutique, etc.

### Recommandation de démarrage (pour aller vite)
- Partir d’une base `akoeleapirest` copiée dans `apps/api`, puis **pruner** les modules inutiles.
- Garder la structure de `main.ts` (prefix, CORS, ValidationPipe, Swagger), en adaptant :
  - les variables d’env attendues (SendGrid/JWT/DB/**Supabase Storage**…)
  - les routes publiques (si besoin)
  - le rate limit (auth OTP, signalements, création annonces)


