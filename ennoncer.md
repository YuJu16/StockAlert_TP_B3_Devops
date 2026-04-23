from weasyprint import HTML

# Defining the Markdown content based on the previous conversion
md_content = """# DevOps — Bachelor 3 Développement · 2025/2026
## TP CI/CD — StockAlert API
### Séance 2 — Pipeline GitHub Actions

---

| Informations clés | |
| :--- | :--- |
| **Durée estimée** | 1h30 |
| **Thème** | GitHub Actions, Docker Hub, Trivy, staging |
| **Travail** | Individuel |

---

## Contexte
Tu rejoins une startup e-commerce. L'équipe dev vient de finir **StockAlert**, une API qui génère des alertes quand le stock d'un produit est critique. Le code est prêt, mais il n'y a aucune automatisation en place.

**Ta mission :** mettre en place la chaîne CI/CD complète. Tu n'écris pas une ligne de code applicatif — tu construis le pipeline autour.

### Repo à forker
> [https://github.com/Jerem-LBN/StockAlert_TP_B3_Devops.git](https://github.com/Jerem-LBN/StockAlert_TP_B3_Devops.git)

**Note :** Forker le repo sur ton compte GitHub avant de commencer. Tout le travail se fait dans ton fork.

### Ce que fait l'application
StockAlert API surveille les niveaux de stock :
* Stock < seuil → alerte warning
* Stock = 0 → alerte critical

**Endpoints disponibles :**
* `GET /health` : état + statistiques alertes
* `GET /products` : liste les produits
* `POST /products` : ajouter un produit
* `PATCH /products/:id/stock` : mettre à jour le stock
* `GET /alerts` : toutes les alertes
* `GET /alerts/active` : alertes non résolues
* `PATCH /alerts/:id/resolve` : résoudre une alerte

---

## Étape 0 • Prérequis (15 min) • Facile
**Objectif : Setup**

Avant d'écrire une seule ligne de YAML :
* Configurer les secrets Docker Hub dans ton fork GitHub.
* Créer la branche `staging` et la pousser sur le remote.
* Vérifier que les tests passent en local.

---

## Étape 1 • GitHub Actions (30 min) • Intermédiaire
**Objectif : Pipeline de base (test et lint)**

1.  Créer le fichier `.github/workflows/ci.yml`.
2.  Le pipeline doit se déclencher sur chaque **push** et chaque **PR** vers `main`.
3.  Il doit comporter deux jobs ordonnés :
    * **Job test :** installe les dépendances et lance les tests.
    * **Job lint :** attend que le job test passe, puis vérifie le style du code.

**Vérification :**
* Pousser sur `main` → les deux jobs doivent être au vert.
* Casser un test et repousser → le job test doit être rouge et le job lint ne doit pas démarrer.

---

## Étape 2 • Docker Hub (30 min) • Intermédiaire
**Objectif : Build Docker sur tag**

Ajouter un job **build** qui se déclenche uniquement sur un tag `v*`. Ce job doit :
* Attendre que les jobs test et lint passent.
* Builder l'image Docker.
* Pusher l'image sur Docker Hub taguée avec le nom du tag Git.

**Vérification :**
* Pousser sur `main` → le job build ne doit pas apparaître.
* Créer et pousser un tag `v1.0.0` → l'image doit être visible sur Docker Hub.

---

## Étape 3 • Sécurité (30 min) • Avancé
**Objectif : Trivy bloquant**

* Modifier le job build pour ajouter un scan de sécurité entre le build et le push.
* Le pipeline doit s'arrêter si une CVE **CRITICAL** est détectée.
* Si le scan passe, le push sur Docker Hub s'exécute normalement.

**Vérification :**
* Pousser un tag → vérifier dans les logs GitHub Actions que Trivy s'exécute bien entre le build et le push.

---

## Étape 4 • CI/CD (20 min) • Avancé
**Objectif : Déploiement staging**

Respecter la logique suivante selon les événements :

| Événement | Jobs attendus |
| :--- | :--- |
| **push main** | test + lint seulement |
| **push staging** | test + lint + build + deploy-staging |
| **tag v\*** | test + lint + build |

**Vérification :**
* Pousser sur `staging` → les 4 jobs doivent tourner.
* Pousser sur `main` → le job `deploy-staging` doit être absent.

---

## Étape 5 • GitHub (10 min) • Facile
**Objectif : Badge de statut**

* Ajouter le badge de statut du pipeline dans le fichier `README.md`.

---

## Critères de réussite

| Critère | Comment vérifier |
| :--- | :--- |
| **Tests passent** | GitHub Actions → job test vert |
| **Pipeline rouge si test cassé** | Casser un test, pousser → job test rouge |
| **Build absent sur push main** | Pousser sur main → pas de job build |
| **Build présent sur tag v\*** | Tagger → job build vert |
| **Trivy entre build et push** | Logs du job build |
| **Image sur Docker Hub** | hub.docker.com → image avec bon tag |
| **Deploy-staging sur push staging** | Pousser sur staging → deploy-staging vert |
| **Deploy-staging absent sur main** | Pousser sur main → pas de deploy-staging |
| **Badge visible** | README affiché sur GitHub |
"""

with open("TP-CICD-StockAlert.md", "w", encoding="utf-8") as f:
    f.write(md_content)