# 🏛️ Bagneux Ma Ville — Guide d'installation Capacitor 6

## Structure du projet

```
bagneux-ma-ville/
├── www/
│   └── index.html          ← L'app (fichier bagneux.html renommé)
├── package.json
├── capacitor.config.json
├── Info.plist.additions.xml ← Clés à copier dans Xcode
└── README.md
```

---

## 1. Installation

```bash
# Dans le dossier du projet
npm install

# Initialiser Capacitor (si pas encore fait)
npx cap init "Bagneux Ma Ville" "com.michel.garlandat.bagneux" --web-dir www

# Ajouter iOS
npx cap add ios
```

---

## 2. Copier l'app HTML

```bash
cp bagneux.html www/index.html
```

---

## 3. Synchroniser avec iOS

```bash
npx cap sync ios
```

---

## 4. Permissions iOS — Info.plist

Dans Xcode, ouvre **ios/App/App/Info.plist** et ajoute les clés
du fichier `Info.plist.additions.xml` :

| Clé | Valeur |
|-----|--------|
| `NSLocationWhenInUseUsageDescription` | "Bagneux Ma Ville utilise votre position pour afficher les services les plus proches." |
| `NSLocationAlwaysAndWhenInUseUsageDescription` | "Bagneux Ma Ville utilise votre position pour vous guider vers les services municipaux." |

---

## 5. Ouvrir dans Xcode

```bash
npx cap open ios
```

---

## 6. Plugins Capacitor utilisés dans l'app

| Plugin | Usage | Import JS |
|--------|-------|-----------|
| `@capacitor/geolocation` | Localiser l'utilisateur sur la carte | `Geolocation.getCurrentPosition()` |
| `@capacitor/browser` | Ouvrir les liens externes (IRIS, Vallée Sud…) | `Browser.open({ url })` |
| `@capacitor/local-notifications` | Rappels collecte déchets | `LocalNotifications.schedule()` |
| `@capacitor/status-bar` | Barre de statut dark sur fond navy | `StatusBar.setStyle()` |
| `@capacitor/splash-screen` | Écran de démarrage gold/navy | Auto via config |

---

## 7. Utiliser les plugins dans index.html

```html
<script type="module">
  // Geolocation
  import { Geolocation } from '@capacitor/geolocation';
  const pos = await Geolocation.getCurrentPosition();

  // Browser (liens externes)
  import { Browser } from '@capacitor/browser';
  await Browser.open({ url: 'https://www.bagneux92.fr' });

  // Notifications locales
  import { LocalNotifications } from '@capacitor/local-notifications';
  await LocalNotifications.schedule({
    notifications: [{
      title: '♻️ Collecte demain',
      body: 'Bac jaune à sortir ce soir',
      id: 1,
      schedule: { at: new Date(Date.now() + 1000 * 60 * 60) }
    }]
  });
</script>
```

> ⚠️ En Capacitor 6, les plugins sont disponibles via `window.Capacitor.Plugins.X`
> dans un fichier HTML classique (sans bundler).
>
> Exemple sans import ES module :
> ```js
> const { Geolocation } = Capacitor.Plugins;
> const pos = await Geolocation.getCurrentPosition();
> ```

---

## 8. App Store Connect

| Champ | Valeur |
|-------|--------|
| Bundle ID | `com.michel.garlandat.bagneux` |
| Nom de l'app | `Bagneux Ma Ville` |
| Catégorie principale | Utilitaires |
| Catégorie secondaire | Référence |
| Âge minimum | 4+ |
| Prix | Gratuit |

**Mots-clés suggérés :**
`bagneux, mairie, 92, hauts-de-seine, déchets, pharmacie, iris, services, ville`

---

## 9. Checklist avant soumission

- [ ] `www/index.html` présent et testé en simulateur
- [ ] Permissions géolocalisation ajoutées dans Info.plist
- [ ] Icônes app générées (1024×1024 + toutes tailles)
- [ ] Splash screen configuré (fond #0a1628)
- [ ] Testé sur iPhone réel (iPhone 13+ recommandé)
- [ ] Safe area notch vérifiée
- [ ] Mode sombre testé
- [ ] Liens externes s'ouvrent dans le navigateur in-app

---

## 10. Commandes utiles

```bash
# Sync après modification de index.html
npx cap sync ios

# Ouvrir Xcode
npx cap open ios

# Lancer en live sur simulateur (avec npx cap run)
npx cap run ios --livereload --external
```
