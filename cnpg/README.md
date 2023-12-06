# Helm chart d'installation de cluster Postgres par l'opérateur cnpg

## Getting Started

Pour déployer un cluster postgres vous pouvez utiliser ce fichier values.yaml

Les paramètres recovery et backup sont désactivé (voir plus bas pour les activer)

Voici les commandes à executer

```bash
helm repo add // ajouter le reste
helm update // ajouter le reste
helm install // ajouter le reste 
```


```yaml
cnpg:
  name: Nom du déploiement du cluster
  instancesNumber: Nombre d'instance dans le cluster
  superuser:
    username: Username du super user
    password: Mot de passe du super user
  app:
    username: Nom de l'utilisateur applicatif
    password: Mot de passe de l'utilisateur applicatif
  initdb:
    database:
      name: Nom de la base de donnée à initialisé
  startDelay: 300
  stopDelay: 300
  primaryUpdateStrategy: unsupervised
  custom_pg_hba:
    enabled: false
    pg_hba:
      - host all all 10.244.0.0/16 md5 
  parameters:
    shared_buffers: 256MB 
    pg_stat_statements:
      max: '10000'
      track: all
      auto_explain.log_min_duration: '10s'
  storage:
    size: 1Gi    
  recovery:
    enabled: false
    s3Path: Path du bucket s3
    s3Endpoint: URI du bucket
    s3Secret:
      name: Nom du secret contenant les identifiant du bucket
      ak_key: Clé contenant l'access key du bucket
      sk_key: Clé contenant le secret key du bucket    
  backup:
    enabled: false
    s3Path: Path du bucket s3
    s3Endpoint: URI du bucket
    s3Secret:
      name: Nom du secret contenant les identifiant du bucket
      ak_key: Clé contenant l'access key du bucket
      sk_key: Clé contenant le secret key du bucket
```

## Restaurer une base de donnée

L'opérateur cnpg permet de restaurer une base de donnée déjà existante. Pour ce faire il faut réaliser un backup de la DB existante en utilisant l'outil barman.

 [Cliquez ici pour plus d'informations](https://www.scaleway.com/en/docs/tutorials/back-up-postgresql-barman/)

Voici la partie du fichier ***values.yaml*** à alimenter pour restaurer une base existante

```yaml
cnpg:
  recovery:
    enabled: true
    s3Path: Path du bucket s3
    s3Endpoint: URI du bucket
    s3Secret:
      name: Nom du secret contenant les identifiant du bucket
      ak_key: Clé contenant l'access key du bucket
      sk_key: Clé contenant le secret key du bucket
```

## Backup de base de donnée

L'opérateur cnpg permet aussi de réaliser un backup régulier du cluster Postgres

Ce backup doit être stocké sur un bucket S3

Voici la partie du fichier ***values.yaml*** à alimenter pour réaliser des backup de votre cluster

```yaml
cnpg:
  backup:
    enabled: true
    s3Path: Path du bucket s3
    s3Endpoint: URI du bucket
    s3Secret:
      name: Nom du secret contenant les identifiant du bucket
      ak_key: Clé contenant l'access key du bucket
      sk_key: Clé contenant le secret key du bucket
```

## Exemple

Vous trouverez un exemple de l'utilisation de ce helm chart en dépendance d'un autre helm sur ce [lien](https://github.com/dnum-mi/dso-tuto-java-helm)
