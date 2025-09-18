# TerraformForAll - Préparation d'environnement

Ce projet utilise Terraform pour déployer des ressources sur AWS.

## Prérequis

### 1. Installation de Terraform

#### Étape 1 : Téléchargement et extraction
1. Téléchargez Terraform depuis [hashicorp.com](https://hashicorp.com)
2. Extrayez le fichier `terraform.exe` dans un dossier (ex: `C:\terraform`)
3. Ajoutez ce dossier à votre PATH système

#### Étape 2 : Ouvrir les Variables d'environnement
1. Appuyez sur `Windows + R`
2. Tapez `sysdm.cpl` et appuyez sur Entrée
3. Cliquez sur l'onglet "Paramètres système avancés"
5. Cliquez sur "Variables d'environnement..."

#### Étape 3 : Modifier le PATH
1. Dans la section "Variables système", trouvez "Path"
2. Sélectionnez "Path" et cliquez sur "Modifier..."
3. Cliquez sur "Nouveau"
4. Ajoutez `C:\terraform`
5. Cliquez sur "OK" partout

### 2. Installation d'AWS CLI

1. Téléchargez l'installateur Windows depuis [https://aws.amazon.com/cli/](https://aws.amazon.com/cli/) (AWS CLI version 2)
2. Exécutez-le (il ajoute le PATH automatiquement)

## Vérification de l'installation

### Vérifier Terraform
```bash
terraform version
```

### Vérifier AWS CLI
```bash
aws --version
```

## Configuration AWS


### 2. Créer un utilisateur IAM

#### Étape 1 : Accès à la console IAM
1. Connectez-vous à la [Console AWS](https://console.aws.amazon.com/)
2. Naviguez vers **IAM** > **Users** > **Add users**

#### Étape 2 : Configuration de l'utilisateur
- **Nom de l'utilisateur** : `terraform-admin` (ou autre nom de votre choix)
- **Type d'accès** :
  - ✅ Cochez **Access key - Programmatic access**
  - ✅ Cochez **AWS Management Console access**
  - Choisissez **Custom password**
  - Décochez **"Require password reset"** si vous voulez éviter le reset au premier login

#### Étape 3 : Ajouter l'utilisateur à un groupe IAM
- Si vous avez déjà un groupe avec les permissions nécessaires (ex. TerraformAdmins), ajoutez-le
- Sinon, créez un nouveau groupe avec une politique comme :

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```

#### Étape 4 : Télécharger les identifiants
Une fois l'utilisateur créé, téléchargez le fichier `.csv` contenant :
- **Access Key ID**
- **Secret Access Key**
- **L'URL de connexion à la console AWS**

### Configuration des credentials AWS avec AWS CLI

Utilisez la commande `aws configure` pour configurer vos accès AWS :

```bash
aws configure
```

Cette commande vous demandera de saisir :
- **AWS Access Key ID** : Votre clé d'accès AWS
- **AWS Secret Access Key** : Votre clé secrète AWS  
- **Default region name** : Votre région AWS (ex: eu-west-3)
- **Default output format** : Format de sortie (laissez vide pour json)

Les informations seront automatiquement sauvegardées dans `~/.aws/credentials` et `~/.aws/config`.

### 4. Connexion à l'interface graphique AWS

1. Utilisez l'URL de console fournie (ex. `https://your-account-id.signin.aws.amazon.com/console`)
2. Connectez-vous avec le nom d'utilisateur et le mot de passe que vous avez définis

### 5. Test de la configuration

Vérifiez que votre configuration fonctionne :
```bash
# Test avec AWS CLI
aws sts get-caller-identity --profile terraform-admin
```

