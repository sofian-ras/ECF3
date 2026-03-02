# Guide Quick Start Docker - ECF3

## Installation en 3 étapes

### 1. Installer Docker Desktop
- Windows/Mac : https://www.docker.com/products/docker-desktop
- Linux : `sudo apt install docker.io docker-compose`

### 2. Lancer le projet
Depuis la racine du dépôt, exécuter :
```bash
cd ECF3
```
Puis lancer :
```bash
docker compose up -d --build
```

**Temps estimé première exécution** : 3-5 minutes (téléchargement image Python + installation dépendances)

### 3. Accéder à Jupyter
- Ouvrir navigateur : http://localhost:8888
- Pas de token/mot de passe requis
- Ouvrir `notebooks/RASMI_sofian_ecf3.ipynb`
- Exécuter toutes les cellules : Kernel > Restart & Run All

## Vérifications

### Docker est bien installé ?
```bash
docker --version
docker compose --version
```

Résultat attendu :
```
Docker version 24.0.x
Docker Compose version v2.x.x
```

### Le conteneur est bien lancé ?
```bash
docker compose ps
```

Résultat attendu :
```
NAME               STATUS        PORTS
ecf3-ml-churn      Up 2 minutes  0.0.0.0:8888->8888/tcp
```

### Java est bien disponible (pour Spark section 5) ?
Dans une cellule du notebook, exécuter :
```python
import os
print(os.environ.get('JAVA_HOME'))
```

Résultat attendu : `/usr/lib/jvm/default-java`

## Commandes utiles

### Arrêter le conteneur
```bash
docker compose down
```

### Voir les logs en temps réel
```bash
docker compose logs -f
```

### Redémarrer après modification
```bash
docker compose restart
```

### Reconstruire l'image (si requirements.txt modifié)
```bash
docker compose build --no-cache
docker compose up -d
```

### Supprimer complètement (conteneur + volumes)
```bash
docker compose down -v
```

## Résolution de problèmes

### Port 8888 déjà utilisé
**Erreur** : `Bind for 0.0.0.0:8888 failed: port is already allocated`

**Solution** : Modifier le port dans `docker-compose.yml` ligne 8 :
```yaml
ports:
  - "8889:8888"  # Changez 8888 par 8889
```

Puis accéder à http://localhost:8889

### Docker Desktop ne démarre pas (Windows)
**Solution** :
1. Activer WSL2 : https://learn.microsoft.com/fr-fr/windows/wsl/install
2. Activer la virtualisation dans le BIOS (VT-x/AMD-V)
3. Redémarrer Docker Desktop

### Permission denied (Linux)
**Solution** : Ajouter votre utilisateur au groupe docker
```bash
sudo usermod -aG docker $USER
newgrp docker
```

### Out of memory
Si le build échoue avec une erreur mémoire, allouer plus de RAM à Docker Desktop :
- Docker Desktop > Settings > Resources > Memory : minimum 4GB

## Architecture du conteneur

```
Container: ecf3-ml-churn
├── OS: Debian Slim
├── Python: 3.11
├── Java: Runtime par défaut Debian (pour PySpark)
├── Dépendances: requirements.txt
│   ├── pandas==2.2.0
│   ├── numpy==1.24.3
│   ├── matplotlib==3.8.2
│   ├── seaborn==0.13.0
│   ├── scikit-learn==1.5.0
│   ├── pyspark==4.1.1
│   ├── scipy==1.11.4
│   └── jupyter==1.0.0
└── Workdir: /workspace (mappe vers votre dossier local)
```

## Avantages Docker pour l'ECF

- **Reproductibilité** : Même environnement exact que l'auteur
- **Pas d'installation locale** : Pas de conflit avec votre Python/Java existant
- **Spark garanti** : Java pré-configuré pour la section 5 bonus
- **Évaluation facilitée** : Le jury lance en une commande
- **Isolation** : Suppression propre sans traces système

## Temps d'exécution typique

- **Build initial** : 3-5 minutes
- **Démarrage conteneur** : 10 secondes
- **Exécution notebook complet** : 2-3 minutes
- **Arrêt** : 2 secondes

---

**Support** : En cas de problème, vérifier d'abord les logs avec `docker compose logs`
