# Journal Docker / Nginx / Let's Encrypt

## 📅 Date : 2025-05-28

### ✅ Objectif du jour
Tester une stack minimale Docker avec :
- Nginx,
- Certbot (Let's Encrypt),
- Volumes montés pour observer les certificats,
- Certificats générés avec la méthode webroot.

Voici donc le texte complet à copier-coller manuellement dans journal.md :

### ✅ Étape : Obtention et installation manuelle d’un certificat Let's Encrypt

- 🔧 **Contexte** : test d’une stack minimale Nginx + Certbot avec persistance des volumes pour le partage des certificats.
- 📁 Dossier de travail : `lets-encrypt-tests`
- 🐳 Docker Compose utilisé avec 2 services :
  - `nginx` pour servir les fichiers et les certificats
  - `certbot` pour obtenir le certificat avec l’option `--webroot`
- 🌍 Domaine : `gitea.demoulin.fr`
- 📁 Points de montage :
  - `./www:/var/www/certbot` (pour les fichiers ACME)
  - `./certbot/conf:/etc/letsencrypt` (pour les certificats)
- 📜 Commande Certbot utilisée :

```bash
docker run --rm -it \
  -v $(pwd)/www:/var/www/certbot \
  -v $(pwd)/certbot/conf:/etc/letsencrypt \
  -v $(pwd)/certbot/www:/var/lib/letsencrypt \
  certbot/certbot certonly \
  --webroot \
  --webroot-path=/var/www/certbot \
  -d gitea.demoulin.fr \
  --email roland@demoulin.fr \
  --agree-tos \
  --no-eff-email
✅ Certificat obtenu avec succès, stocké dans ./certbot/conf/live/gitea.demoulin.fr/
🔐 Test HTTPS OK :
curl -k https://gitea.demoulin.fr
Résultat : HTTPS avec certbot ok !

📅 Date : 2025-05-28


Ensuite, tu peux simplement faire :

```bash
git add journal.md
git commit -m "Ajout du journal de l'obtention manuelle du certificat Let's Encrypt"
git push


## 2025-05-28 - Mise en place HTTPS avec Certbot et Nginx dans Docker

- Correction de la configuration Docker Compose :  
  - Montages de volumes ajustés pour que Nginx serve bien les fichiers de `./www` au bon chemin `/var/www/certbot`.  
  - Ajout du port 443 exposé sur le container Nginx.

- Configuration Nginx mise à jour pour supporter HTTPS avec certificats Let's Encrypt.  

- Obtention du certificat SSL via Certbot en mode `webroot` validée.  

- Test `curl` confirmé : accès HTTPS OK avec certificat valide, redirection HTTP -> HTTPS fonctionnelle.

- Correction d’un problème de chemin d’accès aux fichiers HTML dans le container Nginx (`/var/www/certbot` au lieu de `/var/www/html`).

- Prochaine étape : pousser les changements sur GitHub et maintenir la veille sur le renouvellement automatique des certificats.

---

## 2025-05-28

- Obtention du certificat SSL Let's Encrypt pour gitea.demoulin.fr via Certbot en mode webroot.
- Configuration de Nginx avec redirection HTTP vers HTTPS et utilisation des certificats SSL.
- Test HTTPS réussi avec `curl` (certificat valide et reconnu).
- Le certificat expire le 26 août 2025, soit dans environ 3 mois.
- Prévoir la mise en place d'un renouvellement automatique du certificat avant la date d'expiration (idéalement 30 jours avant).

---

## 🟢 Mercredi 28 mai 2025 – Installation de Gitea via Docker et configuration HTTPS

### 🔧 Objectif
Installer Gitea, accessible en HTTPS via Nginx et un certificat Let's Encrypt valide.

---

### ✅ Étapes réalisées

1. **Ajout du service Gitea dans `docker-compose.yml`**
   - Image utilisée : `gitea/gitea:latest`
   - Ports exposés :
     - 3000 → HTTP
     - 22 (SSH) → désactivé (conflit avec l'hôte, voir ci-dessous)
   - Gitea fonctionne avec une base de données embarquée pour le moment (sqlite).

2. **Erreur rencontrée :**
   - Conflit sur le port `22/tcp` : déjà utilisé par l'hôte (probablement SSH).
   - Solution : ne pas publier le port 22 pour Gitea.
   - Ligne supprimée : `- "22:22"`

3. **Reverse proxy Nginx fonctionnel**
   - Domaine : `https://gitea.demoulin.fr`
   - Redirection automatique HTTP → HTTPS
   - Certificat Let's Encrypt obtenu via Certbot
   - Config nginx modifiée pour pointer vers le conteneur Gitea (port 3000 interne)

4. **Vérification du certificat SSL**
   - Émis par : Let's Encrypt
   - Émis le : 28 mai 2025
   - Expiration : 26 août 2025
   - Commande de vérification :
     ```bash
     curl -v https://gitea.demoulin.fr
     ```

5. **Page de paramétrage Gitea atteinte avec succès**

---

### 📌 Prochaines actions

- [ ] Créer un volume persistant pour Gitea (`gitea:/data`)
- [ ] Éventuellement ajouter une BDD externe (PostgreSQL)
- [ ] Automatiser la configuration initiale via variables d'environnement
- [ ] Planifier le renouvellement du certificat (Certbot)

---

## 28 mai 2025 - Persistance des données Gitea

- Vérification de la persistance des données Gitea :
  - Le dossier `./gitea/data` sur l’hôte contient bien les sous-dossiers `git`, `gitea` et `ssh`.
  - Ces dossiers correspondent aux données persistantes : dépôts Git, configurations et clés SSH.
  - Le volume Docker est bien monté pour persister ces données hors du conteneur.
  - Cela permet de sauvegarder facilement les données et de garder les configurations en cas de redémarrage ou de mise à jour des conteneurs.

- Conclusion : la persistance des données Gitea est opérationnelle et correctement configurée via le volume Docker.
