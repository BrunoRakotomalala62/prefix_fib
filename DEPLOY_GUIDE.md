# 🚀 Guide de Déploiement sur Render - JetX Fibonacci Prediction System

## 📋 Prérequis

1. **Compte Render**: Créez un compte gratuit sur [render.com](https://render.com)
2. **Compte GitHub** (optionnel): Pour déploiement automatique via Git
3. **Fichiers du projet**: Tous les fichiers sont dans ce dossier

## 📦 Structure du Projet

```
jetx-fibonacci-validated/
├── app.py                 # Application Flask principale
├── requirements.txt       # Dépendances Python
├── runtime.txt           # Version Python
├── templates/
│   └── index.html        # Interface web
└── DEPLOY_GUIDE.md       # Ce fichier
```

## 🎯 Méthode 1: Déploiement Direct (Recommandé)

### Étape 1: Préparer les fichiers

1. Téléchargez tous les fichiers du projet
2. Vérifiez que la structure est correcte

### Étape 2: Créer un nouveau Web Service sur Render

1. Connectez-vous à [Render Dashboard](https://dashboard.render.com/)
2. Cliquez sur **"New +"** → **"Web Service"**
3. Choisissez **"Build and deploy from a Git repository"**

### Étape 3: Connecter votre repository

**Option A: Via GitHub**
1. Connectez votre compte GitHub
2. Créez un nouveau repository et uploadez les fichiers
3. Sélectionnez le repository sur Render

**Option B: Upload manuel**
1. Compressez le dossier en ZIP
2. Utilisez l'option "Public Git repository" avec un repo temporaire

### Étape 4: Configuration du Service

Remplissez les champs suivants:

```yaml
Name: jetx-fibonacci-prediction
Region: Frankfurt (EU Central) ou votre région préférée
Branch: main
Root Directory: (laissez vide)
Runtime: Python 3
Build Command: pip install -r requirements.txt
Start Command: gunicorn app:app
```

### Étape 5: Variables d'environnement (optionnel)

Aucune variable d'environnement n'est requise pour la version de base.

### Étape 6: Plan de facturation

Sélectionnez:
- **Free** (pour tester, avec limitations)
- **Starter** ($7/mois, recommandé pour production)

### Étape 7: Déployer

1. Cliquez sur **"Create Web Service"**
2. Render va:
   - Installer les dépendances
   - Lancer l'application
   - Vous donner une URL (ex: `https://jetx-fibonacci-prediction.onrender.com`)

## 🔄 Méthode 2: Déploiement via Git (Automatique)

### 1. Créer un repository Git

```bash
cd jetx-fibonacci-validated
git init
git add .
git commit -m "Initial commit - JetX Fibonacci Prediction v2.0"
```

### 2. Pousser sur GitHub

```bash
# Créez un nouveau repo sur GitHub (exemple: jetx-fibonacci)
git remote add origin https://github.com/VOTRE-USERNAME/jetx-fibonacci.git
git branch -M main
git push -u origin main
```

### 3. Connecter à Render

1. Sur Render Dashboard: **New +** → **Web Service**
2. Autorisez l'accès à GitHub
3. Sélectionnez votre repository
4. Configurez comme dans Méthode 1, Étape 4
5. **Déploiement automatique**: Chaque push sur `main` redéploie l'app

## 🧪 Tester l'Application

Une fois déployée, testez avec:

### Test 1: Page d'accueil
```
Ouvrir: https://VOTRE-APP.onrender.com
```

### Test 2: API Health Check
```bash
curl https://VOTRE-APP.onrender.com/health
```

### Test 3: Calcul de prédiction
```bash
curl -X POST https://VOTRE-APP.onrender.com/calculate \
  -H "Content-Type: application/json" \
  -d '{
    "high1_time": "13:19:16",
    "high1_mult": 4.11,
    "high2_time": "13:19:40",
    "high2_mult": 4.29,
    "current_time": "13:20:00"
  }'
```

## 📊 Exemple de Réponse API

```json
{
  "success": true,
  "input": {
    "high1": {"time": "13:19:16", "multiplier": 4.11},
    "high2": {"time": "13:19:40", "multiplier": 4.29},
    "current_time": "13:20:00"
  },
  "analysis": {
    "interval_measured": 24,
    "fibonacci_interval": 21,
    "difference": 3,
    "precision": "PARFAITE"
  },
  "predictions": [
    {
      "type": "RÉPÉTITION",
      "fibonacci": 21,
      "predicted_time": "13:20:01",
      "zone_start": "13:19:21",
      "zone_end": "13:20:41",
      "probability": "HAUTE",
      "probability_percent": 70,
      "stake": "2-3% bankroll",
      "target_mult": "5-10x"
    },
    ...
  ],
  "recommendations": {
    "primary_option": "RÉPÉTITION",
    "total_stake": "3.5-6% bankroll",
    "coverage_start": "13:19:13",
    "coverage_end": "13:20:54"
  }
}
```

## 🔧 Configuration Avancée

### Utiliser Gunicorn avec plus de workers

Modifiez le **Start Command**:
```
gunicorn --workers 4 --threads 2 app:app
```

### Activer HTTPS (automatique sur Render)

Render fournit automatiquement un certificat SSL gratuit.

### Custom Domain

1. Allez dans **Settings** → **Custom Domain**
2. Ajoutez votre domaine (ex: `jetx.mondomaine.com`)
3. Configurez les DNS selon les instructions

## 📈 Monitoring et Logs

### Voir les logs en temps réel
1. Dashboard Render → Votre service
2. Onglet **"Logs"**

### Métriques
- CPU, Mémoire, Requêtes disponibles dans l'onglet **"Metrics"**

## 🐛 Résolution de Problèmes

### Erreur: "Application failed to start"
```bash
# Vérifiez les logs Render
# Cause commune: dépendances manquantes
pip install -r requirements.txt --dry-run
```

### Erreur: "Module not found"
```bash
# Ajoutez la dépendance manquante à requirements.txt
echo "nom-du-module==version" >> requirements.txt
git commit -am "Fix dependencies"
git push
```

### Application lente (Free plan)
Le plan gratuit met l'app en veille après 15 min d'inactivité.
Solution: Upgrade vers Starter plan ($7/mois)

## 🔐 Sécurité

### 1. Rate Limiting (optionnel)
Ajoutez à `app.py`:
```python
from flask_limiter import Limiter
limiter = Limiter(app, key_func=lambda: request.remote_addr)

@app.route('/calculate', methods=['POST'])
@limiter.limit("30 per minute")
def calculate():
    ...
```

### 2. API Key (optionnel)
Protégez l'API avec une clé:
```python
API_KEY = os.environ.get('API_KEY', 'votre-cle-secrete')

@app.before_request
def check_api_key():
    if request.path != '/' and request.path != '/health':
        key = request.headers.get('X-API-Key')
        if key != API_KEY:
            return jsonify({"error": "Unauthorized"}), 401
```

## 📱 Utilisation de l'API

### Depuis un script Python
```python
import requests

url = "https://VOTRE-APP.onrender.com/calculate"
data = {
    "high1_time": "13:19:16",
    "high1_mult": 4.11,
    "high2_time": "13:19:40",
    "high2_mult": 4.29
}

response = requests.post(url, json=data)
result = response.json()

if result['success']:
    print(f"Prédiction: {result['predictions'][0]['predicted_time']}")
else:
    print(f"Erreur: {result['error']}")
```

### Depuis JavaScript (frontend)
```javascript
fetch('https://VOTRE-APP.onrender.com/calculate', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({
        high1_time: "13:19:16",
        high1_mult: 4.11,
        high2_time: "13:19:40",
        high2_mult: 4.29
    })
})
.then(res => res.json())
.then(data => console.log(data.predictions));
```

## 🎉 Validation Finale

Votre application est déployée avec succès quand:
- ✅ L'URL fonctionne
- ✅ L'interface web s'affiche correctement
- ✅ Les calculs de prédiction retournent des résultats
- ✅ Les logs ne montrent pas d'erreurs

## 📞 Support

En cas de problème:
1. Vérifiez les logs Render
2. Testez en local: `python app.py`
3. Consultez la doc Render: https://render.com/docs

---

**🚀 Bonne chance avec votre déploiement !**

Version: 2.0-validated | Date: 20/02/2026
