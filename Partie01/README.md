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
git clone https://github.com/StartBootstrap/startbootstrap-agency.git

# Copier les fichiers statiques dans le dossier web de Nginx
sudo cp -r /home/ubuntu/frontend/startbootstrap-agency/dist/* /var/www/html/
```

Les fichiers statiques (HTML, CSS, JS) sont maintenant servis directement par Nginx depuis `/var/www/html/`.




- Le chemin `root` doit pointer vers le dossier contenant le fichier `index.html` de votre app Bootstrap.
- Pour obtenir le chemin absolu, utilisez la commande `pwd` dans le dossier voulu.

Redémarrez Nginx :

```bash
sudo systemctl restart nginx
```

## 7. Configuration DNS avec Route53

1. Allez dans AWS Route53 > Hosted zones.
2. Sélectionnez votre zone DNS.
3. Ajoutez un enregistrement de type **A** :
   - **Name** : (ex : `www`)
   - **Value** : IP publique de votre EC2
   - **TTL** : 300 (par défaut)

 Ajoutez un enregistrement de type **A** :
   - **Name** : 
   - **Value** : IP publique de votre EC2
   - **TTL** : 300 (par défaut)

4. Attendez la propagation DNS (quelques minutes à quelques heures).

## 8. Accès à votre application

- Accédez à `http://votre-domaine.com` 
- Vous devriez voir votre site servi par Nginx et sécurisé par SSL.

---

**Résumé des commandes principales :**

```bash
# Connexion SSH
ssh -i /chemin/vers/votre-cle.pem ubuntu@<IP_PUBLIC_EC2>

# Installation
sudo apt update
sudo apt install nginx git -y
sudo apt install certbot python3-certbot-nginx -y

# Déploiement statique
mkdir frontend && cd frontend
git clone https://github.com/StartBootstrap/startbootstrap-agency.git

# (Vérifiez le chemin avec pwd)

# Configuration Nginx
sudo vi /etc/nginx/sites-available/default
sudo systemctl restart nginx
```

