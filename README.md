# FISHY! RETRO V4 — Android Edition

Jeu pixel art hors ligne, 1 doigt, algues, poisson, sons chiptune, pause, record local. Le code du jeu est dans `www/index.html`.

## Jouer dans le navigateur

Ouvrir `www/index.html`. Pour Android, l'application est empaquetée dans une WebView Capacitor, sans serveur externe.

## Compiler automatiquement (GitHub Actions)

1. Créer un dépôt GitHub, puis y importer **tout le contenu de ce dossier**, y compris `.github/workflows/android.yml`, `www/`, `assets/`, `scripts/`, `package.json`, `capacitor.config.json`.
2. Ouvrir **Actions → Build FISHY Android → Run workflow** (ou pousser sur `main`).
3. Attendre la fin du job puis télécharger les artefacts `fishy-retro-debug-apk` et `fishy-retro-release-aab`.
4. Installer l'APK de debug sur le téléphone pour tester. L'AAB sans signature n'est **pas publiable** : configurer la signature ci-dessous.

## Signer l'AAB pour le Play Store

Créer un keystore privé une seule fois, par exemple :

```bash
keytool -genkeypair -v -keystore fishy-release.jks -alias fishy -keyalg RSA -keysize 3072 -validity 10000
```

Ajouter ces **GitHub Actions secrets** (Settings → Secrets and variables → Actions) :

- `ANDROID_KEYSTORE_BASE64` : contenu du fichier `.jks` encodé en base64 **sur une seule ligne** ;
- `ANDROID_KEYSTORE_PASSWORD` : mot de passe du keystore ;
- `ANDROID_KEY_ALIAS` : alias, ex. `fishy` ;
- `ANDROID_KEY_PASSWORD` : mot de passe de la clé.

Ne jamais committer le keystore ou ses mots de passe. Relancer le workflow pour produire un AAB signé. Pour les nouvelles applications, Google Play exige généralement l'inscription à Play App Signing et une version cible Android conforme à ses règles actuelles. Prévoir fiche Play Store, captures, classification du contenu, déclaration de sécurité des données et politique de confidentialité appropriée avant publication.

## Compiler localement

Nécessite Node 22, JDK 21 et Android SDK :

```bash
npm install
npx cap add android
python3 scripts/prepare_android.py
cd android
./gradlew assembleDebug bundleRelease
```

Pour reconstruire après changement du jeu : `npx cap sync android` puis `python3 scripts/prepare_android.py`.

## Remarques

- `com.fishyretro.game` est l'identifiant Android ; le changer **avant la première publication** si nécessaire.
- Le lancement natif utilise l'icône pixel art et un fond turquoise ; Android 12+ impose son propre format de splash.
- Le mode immersif masque les barres système, mais les gestes Android restent disponibles.
- La génération du dossier `android/` est faite par Capacitor lors de la compilation : il n'est pas inclus dans l'archive.
- Cette livraison n'inclut **pas** d'APK/AAB précompilé : les dépendances Android ne sont pas téléchargeables dans cet environnement. Le workflow GitHub doit encore être exécuté et validé.
