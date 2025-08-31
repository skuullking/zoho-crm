# Documentation Architecture Yeddir

## Vue d'ensemble

Ce projet utilise deux architectures distinctes selon le type de page :

1. **Architecture Page d'Accueil** (`index.html`) - Affichage dynamique de toutes les fonctionnalités
2. **Architecture Pages Détail** (ex: `base.html`) - Template modulaire pour pages spécifiques

## Architecture 1 : Page d'Accueil (index.html)

### Fonctionnement

La page d'accueil utilise une **architecture monolithique** avec rendu dynamique complet des contenus.

#### Structure des données

```javascript
const featuresData = {
    automatisation: {
        title: "Automatisation de la force de vente",
        description: "Description globale",
        subFeatures: [
            {
                id: "gestion-leads",
                title: "Gestion des leads",
                description: "Description courte",
                content: "Contenu détaillé affiché sur la page",
                image: "/path/to/image.svg"
            }
            // ... autres sous-fonctionnalités
        ]
    }
    // ... autres fonctionnalités principales
}
```

#### Fonctionnalités clés

- **Navigation principale** : Barre horizontale avec dropdowns au survol
- **Sidebar** : Navigation verticale des sous-fonctionnalités
- **Contenu dynamique** : Sections générées automatiquement
- **Responsive** : Menu burger sur mobile
- **Intersection Observer** : Détection automatique de la section active

### Fonctions principales

#### `setActiveFeature(featureId)`
Change la fonctionnalité principale affichée
```javascript
function setActiveFeature(featureId) {
    activeFeature = featureId;
    renderNavigation();
    renderContent();
}
```

#### `renderContent()`
Génère tout le contenu de la page selon `activeFeature`
```javascript
function renderContent() {
    const currentFeature = featuresData[activeFeature];
    // Mise à jour hero section
    // Génération des sections de fonctionnalités
    // Boutons "En savoir plus" vers pages détail
}
```

#### `renderNavigation()`
Construit la navigation principale et mobile
```javascript
function renderNavigation() {
    // Navigation desktop avec dropdowns
    // Navigation mobile conditionnelle
}
```

### Comment modifier

#### Ajouter une nouvelle fonctionnalité principale

1. **Ajouter dans `mainFeatures`** :
```javascript
const mainFeatures = [
    // ... fonctionnalités existantes
    {
        id: "nouvelle-feature",
        title: "Nouvelle Fonctionnalité"
    }
];
```

2. **Ajouter dans `featuresData`** :
```javascript
const featuresData = {
    // ... features existantes
    "nouvelle-feature": {
        title: "Nouvelle Fonctionnalité",
        description: "Description de la fonctionnalité",
        subFeatures: [
            {
                id: "sous-feature-1",
                title: "Sous-fonctionnalité 1",
                description: "Description courte",
                content: "Contenu détaillé",
                image: "/path/to/image.svg"
            }
        ]
    }
}
```

#### Ajouter une sous-fonctionnalité

Ajouter un objet dans le tableau `subFeatures` de la fonctionnalité concernée :
```javascript
{
    id: "nouvelle-sous-feature",
    title: "Nouvelle Sous-fonctionnalité",
    description: "Description affichée dans le résumé",
    content: "Contenu détaillé affiché dans la section",
    image: "/path/to/media.svg" // .svg, .png, .mp4 supportés
}
```

## Architecture 2 : Pages Détail (Template Modulaire)

### Fonctionnement

Les pages détail utilisent une **architecture modulaire orientée objet** avec classes spécialisées.

#### Classes principales

```javascript
// Gestion de l'état de l'application
class AppState {
    constructor() {
        this.sidebarOpen = true;
        this.isMobile = () => window.innerWidth <= 992;
    }
    toggleSidebar() { /* logique sidebar */ }
}

// Gestion de la navigation
class NavigationManager {
    renderMainNavigation() { /* navigation principale */ }
    renderSidebar() { /* sidebar sous-fonctionnalités */ }
    createDropdown(feature) { /* dropdown hover */ }
}

// Gestion du contenu
class ContentManager {
    renderPageContent() { /* injection contenu dynamique */ }
    renderDynamicContent() { /* contenu spécifique page */ }
}

// Gestion des événements
class EventManager {
    setupSidebarEvents() { /* événements sidebar */ }
    setupMobileMenuEvents() { /* événements mobile */ }
}

// Application principale
class App {
    init() { /* initialisation complète */ }
}
```

#### Configuration centralisée

```javascript
const CONFIG = {
    // Features principales (identique à index.html)
    mainFeatures: [...],
    
    // Sous-features (identique à index.html)
    subFeatures: [...],
    
    // Configuration page actuelle
    currentPage: {
        feature: "aidevente",           // ID de la feature parent
        subFeature: "alertes-notifications", // ID de la sous-feature
        title: "Alertes et notifications",   // Titre affiché
        subtitle: "Description courte",      // Sous-titre
        sidebarTitle: "Aide à la vente"     // Titre sidebar
    }
}
```

### Comment modifier

#### Créer une nouvelle page détail

1. **Copier le template de base**
2. **Modifier la configuration** :
```javascript
CONFIG.currentPage = {
    feature: "marketing",
    subFeature: "segmentation-clients",
    title: "Segmentation des clients",
    subtitle: "Critères de segmentation avancés",
    sidebarTitle: "Automatisation du marketing"
};
```

3. **Personnaliser le contenu dans `ContentManager.renderDynamicContent()`** :
```javascript
renderDynamicContent() {
    const dynamicContent = document.getElementById("dynamicContent");
    
    const content = `
        <div class="detailed-section">
            <h3>Votre contenu personnalisé</h3>
            <p>Description de votre fonctionnalité...</p>
        </div>
        
        <div class="detailed-media">
            <img src="/path/to/image.png" alt="Image" class="detailed-image" />
        </div>
        
        <!-- Autres sections... -->
    `;
    
    dynamicContent.innerHTML = content;
}
```

#### Ajouter des événements spécifiques

Dans `EventManager`, ajouter une méthode :
```javascript
class EventManager {
    init() {
        this.setupSidebarEvents();
        this.setupMobileMenuEvents();
        this.setupResizeEvents();
        this.setupCustomEvents(); // Nouvelle méthode
    }
    
    setupCustomEvents() {
        // Vos événements spécifiques
        document.getElementById('monBouton').addEventListener('click', () => {
            // Logique personnalisée
        });
    }
}
```

## Structure des fichiers

```
projet/
├── index.html                 # Page d'accueil (Architecture 1)
├── index-style.css           # Styles page d'accueil
├── style.css                 # Styles pages détail
├── aidevente/
│   ├── base.html  # Page détail (Architecture 2)
│   ├── calendrier.html
│   └── ...
├── automatisation/
│   └── ...
└── image_video/
    ├── aide-a-la-vente/
    └── ...
```

## Navigation entre pages

### Depuis l'accueil vers le détail
```javascript
// Bouton "En savoir plus" généré automatiquement
moreBtn.href = `/${activeFeature}/${feature.id}.html`;
```

### Depuis le détail vers l'accueil
```javascript
// Navigation principale
button.onclick = () => window.location.href = "/index.html";
```

### Entre pages détail
```javascript
// Sidebar navigation
btn.onclick = () => window.location.href = `/aidevente/${sf.id}.html`;
```

## Responsive Design

### Breakpoints
- **Desktop** : > 992px - Sidebar visible par défaut
- **Mobile** : ≤ 992px - Sidebar en overlay, menu burger

### Adaptation automatique
```javascript
const isMobile = () => window.innerWidth <= 992;

window.addEventListener("resize", () => {
    // Logique d'adaptation automatique
    this.appState.handleResize();
    this.navigationManager.renderMainNavigation();
});
```

## Classes CSS importantes

### Layout principal
```css
.header              /* En-tête fixe */
.sidebar             /* Sidebar navigation */
.main-content        /* Contenu principal */
.container           /* Conteneur centré */
```

### Navigation
```css
.nav-container       /* Navigation horizontale */
.dropdown-menu       /* Menus déroulants */
.mobile-nav-menu     /* Menu mobile */
.sidebar-nav         /* Navigation sidebar */
```

### Contenu
```css
.detailed-hero       /* Section hero pages détail */
.detailed-content    /* Container contenu détail */
.detailed-section    /* Sections de contenu */
.feature-section     /* Sections fonctionnalités accueil */
```


## Bonnes pratiques

### Ajout de nouvelles fonctionnalités

1. **Toujours maintenir la cohérence** entre les deux architectures
2. **Respecter la structure des données** existante
3. **Tester la responsivité** sur différentes tailles d'écran
4. **Vérifier la navigation** entre toutes les pages

### Performance

1. **Images optimisées** : Utiliser des formats appropriés (SVG pour icônes, WebP pour photos)
2. **Lazy loading** : Considérer pour les images lourdes
3. **Debouncing** : Utiliser pour les événements de redimensionnement

### Accessibilité

1. **Attributs ARIA** : `aria-label`, `aria-expanded`
2. **Navigation clavier** : Support des touches Tab/Enter
3. **Contraste** : Respecter les normes WCAG
4. **Textes alternatifs** : Sur toutes les images

## Maintenance

### Ajout d'une nouvelle page détail

1. Copier le template de base
2. Modifier `CONFIG.currentPage`
3. Personnaliser `ContentManager.renderDynamicContent()`
4. Ajouter les styles spécifiques si nécessaire
5. Tester la navigation depuis/vers cette page

### Modification du design

1. **CSS global** : Modifier `style.css` ou `index-style.css`
2. **Composants spécifiques** : Ajouter classes dans les templates
3. **Responsive** : Tester sur toutes les tailles d'écran

### Ajout de nouvelles données

1. **Page d'accueil** : Modifier `featuresData`
2. **Pages détail** : Modifier `CONFIG.mainFeatures` et `CONFIG.subFeatures`
3. **Maintenir la cohérence** entre les deux structures

## Support navigateurs

- Chrome 80+
- Firefox 75+
- Safari 13+
- Edge 80+

## Dépendances

- **CSS** : Google Fonts (Inter)
- **JavaScript** : Vanilla JS (ES6+)
- **Images** : SVG, PNG, WebP, MP4

## Troubleshooting

### Problèmes courants

**Navigation ne fonctionne pas**
- Vérifier que les IDs dans la configuration correspondent aux fichiers
- Contrôler la structure des URLs générées

**Sidebar ne s'affiche pas correctement**
- Vérifier les classes CSS appliquées
- Contrôler l'état initial de `sidebarOpen`

**Contenu ne se charge pas**
- Vérifier la structure des données dans `featuresData` ou `CONFIG`
- Contrôler les chemins d'images

**Menu mobile ne fonctionne pas**
- Vérifier les événements sur `burgerMenu`
- Contrôler les classes CSS mobile


Cette documentation vous permet de comprendre, maintenir et étendre efficacement les deux architectures du projet Yeddir.