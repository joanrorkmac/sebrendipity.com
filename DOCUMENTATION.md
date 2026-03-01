# Sebrendipity — Documentation technique

## Table des matieres

1. [Architecture et structure des fichiers](#architecture-et-structure-des-fichiers)
2. [Logique de la structure](#logique-de-la-structure)
3. [Conventions techniques](#conventions-techniques)
4. [Integration Formspree](#integration-formspree)
5. [Deploiement GitHub Pages](#deploiement-github-pages)
6. [Configuration DNS](#configuration-dns)
7. [Maintenance](#maintenance)

---

## Architecture et structure des fichiers

```
/
├── index.html           Accueil / page d'accueil
├── actualite.html       Actualites et evenements
├── boutique.html        Boutique / pieces a vendre
├── portfolio.html       Galerie / portfolio des creations
├── tearoom.html         Tea Room / Livre d'or
├── contact.html         Formulaire de contact et coordonnees
├── merci.html           Page de confirmation post-soumission
├── css/
│   └── style.css        Feuille de style unique et partagee
├── images/              Photos des creations ceramique
├── CNAME                Domaine personnalise GitHub Pages
├── DOCUMENTATION.md     Ce fichier
└── README.md            README du depot
```

## Logique de la structure

### Pourquoi des fichiers HTML a la racine ?

GitHub Pages sert les fichiers statiques depuis la racine du depot. En placant tous les `.html` a la racine, les URL sont directement previsibles :

- `sebrendipity.com/` → `index.html`
- `sebrendipity.com/actualite.html`
- `sebrendipity.com/boutique.html`
- `sebrendipity.com/portfolio.html`
- `sebrendipity.com/tearoom.html`
- `sebrendipity.com/contact.html`
- `sebrendipity.com/merci.html`

### Pourquoi une seule feuille de style ?

Avec sept pages partageant le meme design system, une feuille de style unique simplifie la maintenance, evite les problemes de specificite, et reduit les requetes HTTP. Les custom properties CSS (variables) centralisent les tokens de design (couleurs, typographie, espacements).

### Pourquoi pas de JavaScript ?

Le site est un portfolio et une vitrine artisanale. Il n'y a pas de logique cote client necessaire. Le formulaire utilise la soumission HTML native vers Formspree. Cette absence de JS garantit :

- Performance maximale (pas de bundle a charger)
- Accessibilite native (pas de widgets JS inaccessibles)
- Securite (pas de surface d'attaque cote client)
- Maintenabilite (aucune dependance a mettre a jour)

## Conventions techniques

- **Langue** : `lang="fr"` sur toutes les balises `<html>`
- **Encodage** : UTF-8
- **Viewport** : `width=device-width, initial-scale=1.0`
- **Approche responsive** : mobile-first (styles de base = mobile, media queries progressives a 40rem et 64rem)
- **Accessibilite** : skip-nav, aria-current, aria-label sur nav, labels sur tous les champs, hierarchie de titres h1→h2→h3
- **Nommage CSS** : BEM-inspired mais simplifie (`.site-header`, `.form-group`, `.page-hero`, `.section--alt`, `.gallery-grid`, `.card-grid`)
- **Unites** : `rem` pour la typographie et les espacements, `px` uniquement pour les bordures

## Integration Formspree

- **Endpoint** : `https://formspree.io/f/VOTRE_ID` (a remplacer par l'ID reel apres creation du formulaire sur formspree.io)
- **Methode** : POST (soumission HTML native)
- **Redirection** : champ cache `_next` pointant vers `merci.html`
- **Anti-spam** : champ honeypot `_gotcha` cache via CSS
- **Sujet** : champ cache `_subject` avec valeur fixe

### Champs du formulaire de contact

| Champ | `name` | Type | Requis |
|-------|--------|------|--------|
| Nom | `name` | text | oui |
| E-mail | `email` | email | oui |
| Telephone | `phone` | tel | non |
| Sujet | `subject` | select | oui |
| Message | `message` | textarea | oui |

## Deploiement GitHub Pages

### Etapes

1. Creer un depot GitHub
2. Pousser tout le code a la racine du depot
3. Aller dans **Settings → Pages**
4. Source : **Deploy from a branch**
5. Branch : `main` / dossier `/ (root)`
6. Sauvegarder
7. Le fichier `CNAME` est automatiquement lu par GitHub Pages
8. GitHub Pages provisionne automatiquement un certificat SSL Let's Encrypt

### Verification

- `https://<username>.github.io/<repo>/` doit fonctionner immediatement
- `https://sebrendipity.com/` fonctionnera une fois le DNS configure

## Configuration DNS

Le domaine `sebrendipity.com` doit etre pointe vers GitHub Pages :

### Enregistrements A (apex domain)

```
Type : A    Sous-domaine : (vide)    Cible : 185.199.108.153    TTL : 3600
Type : A    Sous-domaine : (vide)    Cible : 185.199.109.153    TTL : 3600
Type : A    Sous-domaine : (vide)    Cible : 185.199.110.153    TTL : 3600
Type : A    Sous-domaine : (vide)    Cible : 185.199.111.153    TTL : 3600
```

### Enregistrement CNAME (sous-domaine www)

```
Type : CNAME    Sous-domaine : www    Cible : <username>.github.io.    TTL : 3600
```

### Enregistrements AAAA (IPv6)

```
Type : AAAA    Sous-domaine : (vide)    Cible : 2606:50c0:8000::153    TTL : 3600
Type : AAAA    Sous-domaine : (vide)    Cible : 2606:50c0:8001::153    TTL : 3600
Type : AAAA    Sous-domaine : (vide)    Cible : 2606:50c0:8002::153    TTL : 3600
Type : AAAA    Sous-domaine : (vide)    Cible : 2606:50c0:8003::153    TTL : 3600
```

## Maintenance

### Mise a jour du contenu

Modifier directement les fichiers HTML. Chaque page est autonome et lisible. Rechercher le texte a modifier, le changer, commit, push.

### Mise a jour du design

Toutes les valeurs visuelles sont centralisees dans les custom properties CSS en haut de `css/style.css`. Modifier une couleur ou une taille de police se fait en un seul endroit.

### Ajout d'une page

1. Dupliquer un fichier HTML existant
2. Modifier le contenu dans `<main>`
3. Mettre a jour le `<title>` et la `<meta name="description">`
4. Mettre a jour `aria-current="page"` dans la navigation
5. Ajouter le lien dans la navigation de **tous** les fichiers HTML

### Ajout d'images

1. Placer les images dans le dossier `images/`
2. Utiliser des noms de fichiers en minuscules, sans espaces (tirets a la place)
3. Ajouter une balise `<figure class="gallery-item">` dans le HTML avec un `alt` descriptif

### Gestion du livre d'or (Tea Room)

Le livre d'or fonctionne en deux temps :

1. **Reception** : Les visiteurs soumettent un message via le formulaire. Le message est envoye par e-mail via Formspree.
2. **Publication** : Pour publier un message, ouvrir `tearoom.html`, dupliquer un bloc `<blockquote class="guestbook-entry">...</blockquote>`, modifier le texte, le nom et la date, puis commit et push.

Le CAPTCHA est une question mathematique simple validee par l'attribut HTML `pattern`. Il ne necessite aucun JavaScript.

### Surveillance

- Verifier regulierement que le certificat SSL est actif
- Tester le formulaire Formspree periodiquement
- Valider l'accessibilite avec l'extension WAVE ou axe DevTools
