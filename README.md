# 🏠 DPE Pro - 2A Immo

Application web de prospection immobilière basée sur les données DPE (Diagnostic de Performance Énergétique) pour l'agence 2A Immo.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Firebase](https://img.shields.io/badge/Firebase-10.7.1-orange.svg)](https://firebase.google.com/)
[![Leaflet](https://img.shields.io/badge/Leaflet-1.9.4-green.svg)](https://leafletjs.com/)

## 📋 Description

DPE Pro est une application de prospection terrain permettant aux agents immobiliers de :
- 🔍 Rechercher des biens par code postal, année et classe énergétique
- 🗺️ Visualiser les biens sur une carte interactive
- 📍 Géolocaliser précisément toutes les adresses
- ✅ Marquer les biens visités et intéressants
- 🎯 Générer des missions de prospection optimisées
- 📊 Suivre ses statistiques et performances
- 👥 Collaborer en équipe avec synchronisation en temps réel

## ✨ Fonctionnalités principales

### 🔍 Recherche avancée
- Recherche par code(s) postal(aux) multiple(s)
- Filtrage par année et mois
- Filtrage par classe énergétique (A-G)
- Recherche géolocalisée avec rayon personnalisable (100m - 10km)

### 🗺️ Cartographie intelligente
- Géolocalisation automatique de toutes les adresses via API gouvernementale
- Cache persistant pour performances optimales
- Marqueurs colorés par statut (nouveau, intéressé, visité, collègue, brûlé)
- Clustering automatique des marqueurs
- Mode heatmap
- Mode radar pour prospection terrain

### 🎯 Gestion de mission
- Génération automatique d'itinéraires optimisés (algorithme du plus proche voisin)
- Calcul des distances et temps de trajet
- Export PDF des missions
- Intégration Google Maps pour navigation

### 📊 Statistiques et achievements
- Suivi des recherches, visites et intéressés
- Taux de conversion
- Système de badges et récompenses
- Historique des activités

### 👥 Collaboration équipe
- Authentification multi-utilisateurs (Firebase)
- Synchronisation en temps réel
- Système de statuts partagés
- Dashboard administrateur
- Détection des biens "brûlés" (vus par 2+ personnes)

## 🚀 Démarrage rapide

### Prérequis

Aucune installation nécessaire ! L'application fonctionne entièrement dans le navigateur.

**Navigateurs supportés :**
- Chrome/Edge (recommandé)
- Firefox
- Safari

### Utilisation

1. **Accédez à l'application** : [Lien vers GitHub Pages]
2. **Connectez-vous** avec vos identifiants
3. **Effectuez une recherche** par code postal
4. **Visualisez les résultats** et la carte
5. **Marquez les biens** qui vous intéressent
6. **Générez votre mission** quotidienne

**Note :** Les identifiants vous seront communiqués par votre administrateur.

## 🏗️ Architecture technique

### Technologies utilisées

- **Frontend**: HTML5, CSS3, JavaScript (Vanilla)
- **Cartographie**: Leaflet.js + OpenStreetMap
- **Backend**: Firebase (Authentication + Firestore)
- **API**: 
  - [ADEME DPE API](https://data.ademe.fr/) - Données DPE
  - [API Adresse](https://adresse.data.gouv.fr/) - Géocodage
  - [API Geo](https://geo.api.gouv.fr/) - Données géographiques INSEE

### Structure des données

#### Firestore Collections

```javascript
users/
  {userId}/
    - name: string
    - email: string
    - role: "user" | "admin"
    - stats: object

properties/
  {propertyId}/
    - address: string
    - postalCode: string
    - status: "nouveau" | "interesse" | "visite"
    - seenBy: array
    - notes: array
    - updatedAt: timestamp
```

#### LocalStorage

```javascript
{
  currentUser: object,        // Utilisateur connecté
  dpeData: object,           // Données de statut des biens
  geoCache: object,          // Cache de géolocalisation
  userStats: object,         // Statistiques utilisateur
  cachedDPE: object,         // Cache des recherches DPE
  contacts: array            // Liste de contacts
}
```

## 📱 Fonctionnement hors ligne

L'application utilise le cache navigateur pour fonctionner partiellement hors ligne :
- ✅ Consultation des biens déjà chargés
- ✅ Marquage des statuts (synchronisation à la reconnexion)
- ❌ Nouvelles recherches DPE (nécessite connexion)
- ❌ Géolocalisation de nouvelles adresses (nécessite connexion)

## 🔒 Sécurité et confidentialité

### Données publiques
- Les clés API Firebase sont publiques (normal pour une app web)
- Les données DPE sont publiques (open data gouvernemental)
- La sécurité réelle est assurée par les règles Firestore côté serveur

### Règles Firestore recommandées

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users can only read/write their own data
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Properties are shared but tracked
    match /properties/{propertyId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null;
    }
  }
}
```

## 🎨 Personnalisation

### Couleurs et thème

Les couleurs sont définies en CSS variables dans `:root` :

```css
:root {
    --primary: #04264b;        /* Bleu principal */
    --primary-light: #1e5a8e;  /* Bleu clair */
    --accent: #00a99d;         /* Vert accent */
    --success: #38ef7d;        /* Vert succès */
    --warning: #fdb913;        /* Orange warning */
    --danger: #e31e24;         /* Rouge danger */
}
```

### Configuration Firebase

Pour utiliser votre propre projet Firebase, modifiez la configuration dans `index.html` :

```javascript
const firebaseConfig = {
    apiKey: "VOTRE_API_KEY",
    authDomain: "votre-projet.firebaseapp.com",
    projectId: "votre-projet-id",
    // ...
};
```

## 📊 Performance et optimisations

### Géolocalisation
- ✅ Cache persistant des coordonnées géocodées
- ✅ Limitation à 150ms entre requêtes API
- ✅ Traitement par lots avec progression visuelle
- ✅ Support de 500+ adresses en cache

### Carte
- ✅ Clustering automatique pour > 50 marqueurs
- ✅ Lazy loading des tuiles OpenStreetMap
- ✅ Désactivation auto des couches non visibles

## 🐛 Problèmes connus et solutions

| Problème | Solution |
|----------|----------|
| Carte ne s'affiche pas | Rafraîchir la page (F5) |
| Géolocalisation échoue | Vérifier les permissions du navigateur |
| Données non synchronisées | Vérifier la connexion internet |
| Positions approximatives | Adresses incomplètes dans base DPE |

## 🗺️ Roadmap

### Version actuelle : 4.0
- ✅ Géolocalisation complète
- ✅ Cache persistant
- ✅ Noms de communes INSEE
- ✅ Barre de progression

### À venir (v5.0)
- [ ] Mode sombre
- [ ] Export Excel des résultats
- [ ] Notifications push pour nouveaux DPE
- [ ] Intégration calendrier
- [ ] Chat équipe intégré
- [ ] Photos des biens
- [ ] Notes vocales
- [ ] Signature électronique

## 🤝 Contribution

Ce projet est privé et destiné à un usage interne 2A Immo.

Pour toute suggestion ou bug, contactez : contact@2a-immobilier.fr

## 📄 License

Copyright © 2024-2025 2A Immo. Tous droits réservés.

Usage interne uniquement.

## 👥 Équipe

- **Développement** : Aurélie 
- **Product Owner** : 2A Immo
- **Utilisateurs** : Maud, Sonia, Aurélie

## 📞 Support

- 📧 Email : contact@2a-immobilier.fr
- 🌐 Site web : www.2a-immobilier.fr
- 📱 Téléphone : 06.86.38.62.59

---

<div align="center">
  <strong>Fait avec ❤️ pour 2A Immo</strong>
  <br><br>
  <img src="https://img.shields.io/badge/Prospection-DPE-blue" alt="DPE">
  <img src="https://img.shields.io/badge/Made%20in-France-blue" alt="France">
  <img src="https://img.shields.io/badge/Status-Production-success" alt="Status">
</div>
