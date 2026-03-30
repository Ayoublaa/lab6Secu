# Rapport d'Analyse Statique Android — DIVA APK
> Lab de sécurité mobile — Analyse statique avec MobSF

![Security Score](https://img.shields.io/badge/Security%20Score-36%2F100-red)
![MobSF](https://img.shields.io/badge/MobSF-v4.0.6-blue)
![APK](https://img.shields.io/badge/APK-DIVA%20v1.0-orange)
![Status](https://img.shields.io/badge/Status-Terminé-green)

---

## Table des matières

- [Informations générales](#informations-générales)
- [Task 1 — Préparation de l'environnement](#task-1--préparation-de-lenvironnement)
- [Task 2 — Lancement de MobSF](#task-2--lancement-de-mobsf)
- [Task 3 — Import et analyse de l'APK](#task-3--import-et-analyse-de-lapk)
- [Task 4 — Analyse du manifeste et des permissions](#task-4--analyse-du-manifeste-et-des-permissions)
- [Task 5 — Analyse de la configuration réseau](#task-5--analyse-de-la-configuration-réseau)
- [Task 6 — Analyse du code et des ressources](#task-6--analyse-du-code-et-des-ressources)
- [Task 7 — Corrélation OWASP MASVS](#task-7--corrélation-owasp-masvs)
- [Rapport d'audit final](#rapport-daudit-final)
- [Recommandations prioritaires](#recommandations-prioritaires)
- [Annexes](#annexes)

---

## Informations générales

| Champ | Valeur |
|-------|--------|
| **Date d'analyse** | 29 Mars 2026 |
| **Analyste** | Etudiant |
| **APK analysé** | DivaApplication.apk (DIVA — Damn Insecure and Vulnerable App) |
| **Version APK** | 1.0 |
| **Source** | https://github.com/payatu/diva-android |
| **Outil principal** | MobSF v4.0.6 |
| **Environnement** | VM Mobexler — Ubuntu 22.04 Jammy Jellyfish |
| **SHA-256** | `5cefc51fce9bd760b92ab2340477f4dda84b4ae0c5d04a8c9493e4fe34fab7c5` |
| **MD5** | `82ab8b2193b3cfb1c737e3a786be363a` |
| **SHA-1** | `27e849d9d7b86a3a3357fb3e980433a91d416801` |
| **Taille** | 1.43 MB |
| **Score de sécurité** | **36 / 100** |

> **Périmètre légal** : Cette analyse s'inscrit dans un cadre strictement pédagogique. L'APK DIVA est une application volontairement vulnérable conçue à des fins de formation. Aucune exploitation des vulnérabilités n'a été réalisée.

---

## Task 1 — Préparation de l'environnement

### Objectif
Créer un environnement de travail organisé et traçable pour l'analyse.

### Étapes réalisées

```bash
# Création du dossier de travail daté
mkdir -p ~/apk_analysis/2026-03-29
cd ~/apk_analysis/2026-03-29

# Téléchargement de l'APK DIVA via Firefox dans la VM
mv ~/Downloads/DivaApplication.apk ~/apk_analysis/2026-03-29/app-debug.apk

# Vérification intégrité
sha256sum app-debug.apk > apk_hash.txt
# Résultat : 5cefc51fce9bd760b92ab2340477f4dda84b4ae0c5d04a8c9493e4fe34fab7c5

ls -lh app-debug.apk
# Résultat : 1.5M

# Fichier de traçabilité
echo "Date d'analyse : Sun 29 Mar 2026 07:55:30 PM EDT" > analyse_info.txt
echo "Analyste : Etudiant" >> analyse_info.txt
echo "APK analysé : DivaApplication.apk (DIVA)" >> analyse_info.txt
echo "Source : https://github.com/payatu/diva-android" >> analyse_info.txt
cat apk_hash.txt >> analyse_info.txt
```

### Screenshot — Hash SHA-256 et fichier de traçabilité

![Task 1 - Fichier traçabilité](screenshots/fichierTracabilite.png)

### Résultats
- Dossier de travail : `~/apk_analysis/2026-03-29/`
- APK disponible : `app-debug.apk` (1.5 MB)
- Hash SHA-256 documenté
- Fichier `analyse_info.txt` créé

---

## Task 2 — Lancement de MobSF

### Objectif
Démarrer MobSF et vérifier l'accessibilité de l'interface web.

### Étapes réalisées

MobSF lancé depuis le menu d'applications Mobexler.

```
[INFO] Mobile Security Framework v4.0.6
Default Credentials: mobsf/mobsf
[INFO] Listening at: http://0.0.0.0:8000
```

Accès Firefox : `http://127.0.0.1:8000` — identifiants `mobsf / mobsf`

```bash
echo "MobSF version : v4.0.6" >> ~/apk_analysis/2026-03-29/analyse_info.txt
```

### Screenshot — Page de connexion MobSF

![Task 2 - MobSF Login](screenshots/mobsf.png)

### Résultats
- MobSF v4.0.6 opérationnel
- Interface web accessible sur `http://127.0.0.1:8000`

---

## Task 3 — Import et analyse de l'APK

### Objectif
Soumettre l'APK à MobSF pour analyse statique complète.

### Screenshot — Upload de l'APK

![Task 3 - Upload APK](screenshots/uploadApk.png)

### Screenshot — Page de résultats MobSF (score 36/100)

![Task 3 - Résultats analyse](screenshots/Analysedeapk.png)

### Résultats

| Métrique | Valeur |
|----------|--------|
| Security Score | **36/100** |
| Trackers détectés | 0/432 |
| Activités | 17 (dont 2 exportées) |
| Services | 0 |
| Providers | 1 (exporté) |
| Target SDK | 23 (Android 6.0) |
| Min SDK | 15 (Android 4.0.3) |

---

## Task 4 — Analyse du manifeste et des permissions

### Objectif
Examiner le manifeste Android et identifier les configurations sensibles.

### Screenshot — Manifest Analysis

![Task 4 - Manifest Analysis](screenshots/ManifestAnalisis.png)

### Vulnérabilités identifiées

| N° | Problème | Sévérité | Détail |
|----|----------|----------|--------|
| 1 | `android:debuggable=true` | **HIGH** | Permet l'attachement d'un débogueur |
| 2 | MinSDK = 15 (Android 4.0.3) | **HIGH** | OS non patché, vulnérabilités non corrigées |
| 3 | `android:allowBackup=true` | WARNING | Backup ADB possible via USB |
| 4 | `APICredsActivity` exportée | WARNING | Accessible par toute application tierce |
| 5 | `APICreds2Activity` exportée | WARNING | Accessible par toute application tierce |
| 6 | `NotesProvider` exporté | WARNING | Content Provider sans permission |

---

## Task 5 — Analyse de la configuration réseau

### Objectif
Examiner la sécurité des communications réseau.

### Observations

| Élément | Valeur | Risque |
|---------|--------|--------|
| `network_security_config.xml` | **Absent** | Aucune politique réseau |
| `usesCleartextTraffic` | Non défini (SDK 23) | HTTP autorisé par défaut |
| Certificate pinning | **Absent** | Pas de vérification de certificat |
| HTTPS enforcement | **Absent** | Pas de forçage TLS |

> Target SDK 23 — Android autorise le trafic HTTP en clair par défaut à ce niveau d'API.

---

## Task 6 — Analyse du code et des ressources

### Objectif
Identifier les vulnérabilités dans le code source et les bibliothèques natives.

### Screenshot — Code Analysis

![Task 6 - Code Analysis](screenshots/codeAnalysis.png)

### Screenshot — Binary Analysis

![Task 6 - Binary Analysis](screenshots/BinaryAnalysis.png)

### Screenshot — Vulnérabilités documentées dans le terminal

![Task 6 - Terminal vulnérabilités](screenshots/termVelunirabite.png)

### Code Analysis — 5 vulnérabilités

| N° | Issue | Sévérité | CWE | MASVS |
|----|-------|----------|-----|-------|
| 1 | Injection SQL — SQLite raw queries | WARNING | CWE-89 | MSTG-CODE-4 |
| 2 | Debug configuration activée | **HIGH** | CWE-919 | MSTG-RESILIENCE-2 |
| 3 | Logs — infos sensibles | INFO | CWE-532 | MSTG-STORAGE-3 |
| 4 | Fichiers temporaires non sécurisés | WARNING | CWE-276 | MSTG-STORAGE-2 |
| 5 | Écriture sur stockage externe | WARNING | CWE-276 | MSTG-STORAGE-2 |

### Binary Analysis — Bibliothèques natives `libdivajni.so`

| Protection | x86 | armeabi-v7a | armeabi | arm64-v8a |
|------------|-----|-------------|---------|-----------|
| NX bit | True | **False** | **False** | **False** |
| Stack Canary | True | **False** | **False** | **False** |
| FORTIFY | **False** | **False** | **False** | **False** |
| Symbols Stripped | **False** | **False** | **False** | **False** |
| RELRO | Full | Full | Full | Full |

---

## Task 7 — Corrélation OWASP MASVS

### Tableau de corrélation complet

| Vulnérabilité | Référence MASVS | Preuve | Impact |
|---------------|-----------------|--------|--------|
| `android:debuggable=true` | **MSTG-RESILIENCE-2** | `BuildConfig.java` | Dump mémoire, accès débogueur |
| Injection SQL SQLite | **MSTG-CODE-4** | `SQLInjectionActivity.java` | Accès/modification BDD |
| Stockage externe non sécurisé | **MSTG-STORAGE-2** | `InsecureDataStorage4Activity.java` | Données lisibles par toute app |
| Logs applicatifs sensibles | **MSTG-STORAGE-3** | Fichiers log | Fuite d'informations |
| Activités exportées non protégées | **MSTG-PLATFORM-1** | `APICredsActivity`, `APICreds2Activity` | Accès non autorisé |
| Absence network_security_config | **MSTG-NETWORK-2** | Manifest | Interception trafic HTTP |
| NX bit absent (bibliothèques) | **MSTG-CODE-9** | `libdivajni.so` (arm) | Exploitation mémoire |
| MinSDK = 15 | **MSTG-PLATFORM-1** | Manifest | OS sans patches sécurité |

### Tests MASTG complémentaires

1. **MASTG-TEST-0001** — Testing Local Data Storage : vérifier le chiffrement des données locales (SQLite, SharedPreferences, fichiers)
2. **MASTG-TEST-0020** — Testing for Injection Flaws : tester manuellement les points d'injection dans `SQLInjectionActivity.java`

---

## Rapport d'audit final

### Résumé exécutif

L'analyse statique de l'application **DIVA v1.0** révèle un niveau de risque **élevé** (score MobSF : **36/100**). Les principales vulnérabilités concernent :
- La configuration de débogage active (`android:debuggable=true`)
- Un stockage de données non sécurisé (fichiers temp, stockage externe, SQLite sans protection)
- Des composants Android exportés sans contrôle d'accès
- Des bibliothèques natives dépourvues des protections mémoire standard (NX, stack canary)
- Un niveau d'API cible obsolète (SDK 23)

Ces problèmes représentent des risques concrets de compromission des données utilisateur et d'accès non autorisé à l'application.

---

### Vulnérabilité 1 — Mode debug activé en production

| Champ | Détail |
|-------|--------|
| **Sévérité** | Élevée |
| **MASVS** | MSTG-RESILIENCE-2 |
| **CWE** | CWE-919 |
| **Preuve** | `BuildConfig.java` — Manifest : `android:debuggable="true"` |
| **Impact** | Dump mémoire, inspection stack trace, extraction de données sensibles |
| **Remédiation** | `buildTypes { release { debuggable false } }` dans Gradle |

---

### Vulnérabilité 2 — Injection SQL

| Champ | Détail |
|-------|--------|
| **Sévérité** | Moyenne/Élevée |
| **MASVS** | MSTG-CODE-4 / OWASP M7 |
| **CWE** | CWE-89 |
| **Preuve** | `InsecureDataStorage2Activity.java`, `SQLInjectionActivity.java`, `NotesProvider.java` |
| **Impact** | Accès non autorisé à la BDD, modification/suppression de données |
| **Remédiation** | Requêtes paramétrées via `SQLiteDatabase.query()` ou `PreparedStatement` |

---

### Vulnérabilité 3 — Stockage de données non sécurisé

| Champ | Détail |
|-------|--------|
| **Sévérité** | Moyenne |
| **MASVS** | MSTG-STORAGE-2 |
| **CWE** | CWE-276 |
| **Preuve** | `InsecureDataStorage3Activity.java`, `InsecureDataStorage4Activity.java` |
| **Impact** | Données lisibles par toute application installée sur l'appareil |
| **Remédiation** | Utiliser `Context.getFilesDir()` + chiffrement Android Keystore |

---

### Vulnérabilité 4 — Composants exportés sans protection

| Champ | Détail |
|-------|--------|
| **Sévérité** | Moyenne |
| **MASVS** | MSTG-PLATFORM-1 |
| **CWE** | CWE-926 |
| **Preuve** | `APICredsActivity`, `APICreds2Activity`, `NotesProvider` — intent-filter sans permission |
| **Impact** | Application malveillante peut accéder aux credentials et aux notes |
| **Remédiation** | `android:exported="false"` ou définir une permission custom |

---

### Vulnérabilité 5 — Bibliothèques natives sans protections mémoire

| Champ | Détail |
|-------|--------|
| **Sévérité** | Moyenne |
| **MASVS** | MSTG-CODE-9 |
| **Preuve** | Binary Analysis — NX: False, Stack Canary: False, FORTIFY: False (armeabi, arm64) |
| **Impact** | Exploitation de vulnérabilités mémoire facilitée, reverse engineering simplifié |
| **Remédiation** | Recompiler avec `-fstack-protector-all`, `-z noexecstack`, `-D_FORTIFY_SOURCE=2` |

---

## Recommandations prioritaires

### Priorité 1 — Immédiat

1. **Désactiver le mode debug** : `debuggable false` dans le build release Gradle
2. **Monter le Target SDK à 33+** : Migrer vers Android 13+ pour les dernières protections
3. **Corriger les injections SQL** : Remplacer les requêtes brutes par des requêtes paramétrées

### Priorité 2 — Court terme

4. **Sécuriser le stockage** : Migrer vers le stockage interne chiffré, supprimer les écritures sur stockage externe pour les données sensibles
5. **Protéger les composants exportés** : Ajouter `android:exported="false"` ou une permission sur les 3 composants exposés
6. **Ajouter une configuration réseau** : `network_security_config.xml` avec `cleartextTrafficPermitted="false"`

### Priorité 3 — Moyen terme

7. **Recompiler les libs natives** avec protections mémoire (stack canary, NX bit, FORTIFY, strip symbols)
8. **Supprimer les logs sensibles** : Retirer les `Log.d()` / `Log.v()` contenant des données utilisateur
9. **Signature v2/v3** : Mettre à jour la chaîne de build APK

---

## Annexes

### A. Composants exportés

| Composant | Type | Protection | Risque |
|-----------|------|------------|--------|
| `jakhar.aseem.diva.APICredsActivity` | Activity | Aucune | Accès credentials API |
| `jakhar.aseem.diva.APICreds2Activity` | Activity | Aucune | Accès credentials API |
| `jakhar.aseem.diva.NotesProvider` | Content Provider | Aucune | Lecture/écriture notes |

### B. Fichiers sensibles identifiés

| Fichier | Problème |
|---------|----------|
| `jakhar/aseem/diva/SQLInjectionActivity.java` | Injection SQL |
| `jakhar/aseem/diva/InsecureDataStorage2Activity.java` | Stockage non sécurisé + SQLi |
| `jakhar/aseem/diva/InsecureDataStorage3Activity.java` | Fichiers temporaires |
| `jakhar/aseem/diva/InsecureDataStorage4Activity.java` | Stockage externe |
| `jakhar/aseem/diva/NotesProvider.java` | Provider exporté + SQLi |
| `jakhar/aseem/diva/BuildConfig.java` | Debug activé |

### C. Hash de l'APK

```
SHA-256 : 5cefc51fce9bd760b92ab2340477f4dda84b4ae0c5d04a8c9493e4fe34fab7c5
SHA-1   : 27e849d9d7b86a3a3357fb3e980433a91d416801
MD5     : 82ab8b2193b3cfb1c737e3a786be363a
```

### D. Ressources officielles

| Ressource | URL |
|-----------|-----|
| MobSF | https://github.com/MobSF/Mobile-Security-Framework-MobSF |
| OWASP MASVS | https://mas.owasp.org/MASVS/ |
| OWASP MASTG | https://mas.owasp.org/MASTG/ |
| Android Security | https://source.android.com/docs/security |
| Network Security Config | https://developer.android.com/privacy-and-security/security-config |
| Android Security Best Practices | https://developer.android.com/privacy-and-security/security-best-practices |

---

### E. Checklist de fin de séance

- [x] VM Mobexler démarrée et fonctionnelle
- [x] APK DIVA téléchargé et vérifié (SHA-256)
- [x] Dossier de travail organisé (`~/apk_analysis/2026-03-29/`)
- [x] MobSF v4.0.6 lancé et accessible
- [x] APK analysé — score 36/100
- [x] Manifest Analysis complétée
- [x] Code Analysis complétée (5 vulnérabilités)
- [x] Binary Analysis complétée
- [x] Corrélation OWASP MASVS documentée
- [x] Rapport final rédigé
- [x] README GitHub généré avec tous les screenshots

---

### F. Structure du dossier de travail

```
~/apk_analysis/2026-03-29/
├── app-debug.apk              # APK analysé (1.5 MB)
├── apk_hash.txt               # Hash SHA-256
├── analyse_info.txt           # Fichier de traçabilité
├── permissions.txt            # Liste des permissions
├── composants_exportes.txt    # Composants exportés
├── config_reseau.txt          # Configuration réseau
├── vulnerabilites.txt         # Vulnérabilités identifiées
├── correlation_masvs.txt      # Corrélation OWASP MASVS
└── screenshots/
    ├── fichierTracabilite.png
    ├── mobsf.png
    ├── uploadApk.png
    ├── Analysedeapk.png
    ├── ManifestAnalisis.png
    ├── codeAnalysis.png
    ├── BinaryAnalysis.png
    ├── termVelunirabite.png
    └── appetcreedosie.png
```

---

*Rapport généré dans le cadre d'un lab pédagogique de sécurité mobile.*
*Toutes les analyses portent exclusivement sur l'APK DIVA, application open-source conçue pour la formation.*
