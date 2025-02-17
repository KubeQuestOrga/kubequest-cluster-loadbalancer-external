# Flapi - Cluster K3s - Load Balancer External

## 🛠 Tech Stack

- **Nginx**: Utilisé comme proxy inverse pour distribuer le trafic entrant de manière équilibrée entre plusieurs instances de noeud master.
- **Ansible**: Automatise le déploiement de la configuration Nginx et d'autres tâches administratives sur les serveurs (load balancer externes au cluster).
- **CI/CD (GitHub Actions)**: Automatise le processus de déploiment.
- **DNS**: AWS Route 53 est utilisé pour gérer les enregistrements DNS et assurer la haute disponibilité du service. Un health check est configuré pour surveiller l’état des adresses IP des deux load balancer. Si la première IP principale devient indisponible, Route 53 redirige automatiquement le trafic vers la seconde IP, garantissant ainsi une continuité de service sans interruption. Cette configuration permet un basculement automatique en cas de défaillance du premier serveur.

<br /><br /><br /><br />


## 📚 Load Balancer External
- IP (principal) : 149.202.48.239
- IP (secondaire) : 51.178.139.69
- Domain principal : crzcommon.com
- Sous domaine utilisé : cluster-k3s.crzcommon.com
  
<br /><br />


## 📚 Documentation
- Cette section décrit comment installer des équilibreur de charge externe devant les nœuds de serveur d'un cluster K3s à haute disponibilité (HA).
- Ces deux équilibreurs de charge externes seront utilisés pour fournir une adresse d'enregistrement fixe pour l'enregistrement des nœuds ou pour un accès externe au serveur API Kubernetes
- Documentation K3s : https://docs.k3s.io/datastore/cluster-loadbalancer
- Commande pour lancer l'image docker MANUELLEMENT depuis les serveurs physiques :
  ```bash
  sudo docker run -d --restart unless-stopped \
    --name nginx-lb \
    -v /home/debian/loadbalancer-docker/nginx.conf:/etc/nginx/nginx.conf \
    -p 6443:6443 \
    nginx:stable
  ```

<br /><br /><br /><br />


## 🚀 Production

### ⚙️➡️ Processus de distribution automatique (CI / CD)
#### Configuration Initiale pour un Nouveau Projet

1. **Configuration des secrets GitHub**:

   Pour la configuration initiale, assurez-vous d'ajouter les secrets suivants dans votre dépôt GitHub :

   - `CLUSTER_SSH_PRIVATE_KEY`: Votre clé privée SSH. Cette clé doit correspondre à la clé publique déjà installée sur les serveurs cibles.
   - `CLUSTER_SSH_HOSTS`: Les adresses IP des serveurs cibles, séparées par des espaces. <br />
   Exemple : `192.0.2.1 198.51.100.2`. Ces adresses seront utilisées pour préparer le fichier `known_hosts`, facilitant ainsi des connexions SSH sécurisées.

2. **Ajout de la clé publique sur les serveurs cibles**:

   Avant de pouvoir déployer automatiquement via CI/CD, la clé publique associée à `CLUSTER_SSH_PRIVATE_KEY` doit être ajoutée au fichier `~/.ssh/authorized_keys` sur chaque serveur cible. Cette étape garantit que GitHub Actions peut se connecter aux serveurs via SSH sans intervention manuelle.
