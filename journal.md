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
