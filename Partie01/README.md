# Déploiement d'une application Frontend sur une instance EC2 Ubuntu avec Nginx et Certbot

Ce guide explique comment :
- Créer une instance EC2 Ubuntu
- S'y connecter en SSH
- Installer Nginx
- Installer Certbot (Let's Encrypt)
- Déployer une application frontend
- Configurer un enregistrement DNS avec Route53

## 1. Création de l'instance EC2 Ubuntu

1. Connectez-vous à AWS Management Console.
2. Allez dans EC2 > Instances > Lancer une instance.
3. Choisissez l'AMI **Ubuntu Server 22.04 LTS** (ou version récente).
4. Sélectionnez le type d'instance (ex : t2.micro pour les tests).
5. Configurez le réseau et le groupe de sécurité :
   - Autorisez au minimum le port 22 (SSH), 80 (HTTP), 443 (HTTPS).
6. Ajoutez une paire de clés (ou utilisez-en une existante) pour la connexion SSH.
7. Lancez l'instance et notez son **IP publique**.

## 2. Connexion SSH à l'instance EC2

Sous Windows, ouvrez **Git Bash** puis :

```bash
ssh -i /chemin/vers/votre-cle.pem ubuntu@<IP_PUBLIC_EC2>
```
- Remplacez `/chemin/vers/votre-cle.pem` par le chemin réel de votre clé privée.
- Remplacez `<IP_PUBLIC_EC2>` par l'adresse IP publique de l'instance.

## 3. Installation de Nginx

```bash
sudo apt update
sudo apt install nginx -y
```

Vérifiez que Nginx fonctionne :
- Accédez à `http://<IP_PUBLIC_EC2>` dans votre navigateur.
- Vous devriez voir la page "Welcome to Nginx".

## 4. Installation de Certbot (Let's Encrypt)

```bash
sudo apt install certbot python3-certbot-nginx -y
```

Pour générer un certificat SSL (remplacez `votre-domaine.com` par votre domaine) :

```bash
sudo certbot --nginx -d votre-domaine.com -d www.votre-domaine.com
```

Suivez les instructions pour valider le certificat.

## 5. Déploiement de l'application frontend

```bash
mkdir frontend
cd frontend
sudo apt install git -y
git clone <URL_DU_REPO_FRONTEND>
```
- Remplacez `<URL_DU_REPO_FRONTEND>` par l'URL de votre dépôt Git.

Installez les dépendances et construisez votre app (exemple pour React) :

```bash
cd <nom_du_repo>
npm install
npm run build
```

## 6. Configuration de Nginx pour servir l'app frontend

Créez ou modifiez le fichier de configuration Nginx :

```bash
sudo nano /etc/nginx/sites-available/default
```

Exemple de configuration pour servir le dossier `build` :

```
server {
    listen 80;
    server_name votre-domaine.com www.votre-domaine.com;

    root /home/ubuntu/frontend/<nom_du_repo>/build;
    index index.html;

    location / {
        try_files $uri /index.html;
    }
}
```

Redémarrez Nginx :

```bash
sudo systemctl restart nginx
```

## 7. Configuration DNS avec Route53

1. Allez dans AWS Route53 > Hosted zones.
2. Sélectionnez votre zone DNS.
3. Ajoutez un enregistrement de type **A** :
   - **Name** : (ex : `@` ou `www`)
   - **Value** : IP publique de votre EC2
   - **TTL** : 300 (par défaut)
4. Attendez la propagation DNS (quelques minutes à quelques heures).

## 8. Accès à votre application

- Accédez à `http://votre-domaine.com` ou `https://votre-domaine.com`.
- Vous devriez voir votre application frontend servie par Nginx et sécurisée par SSL.

---

**Résumé des commandes principales :**

```bash
# Connexion SSH
ssh -i /chemin/vers/votre-cle.pem ubuntu@<IP_PUBLIC_EC2>

# Installation
sudo apt update
sudo apt install nginx git -y
sudo apt install certbot python3-certbot-nginx -y

# Déploiement
mkdir frontend && cd frontend
git clone <URL_DU_REPO_FRONTEND>
cd <nom_du_repo>
npm install && npm run build
```

N'hésitez pas à adapter ce guide selon vos besoins spécifiques !
