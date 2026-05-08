# LAB10_Guide-d-installation-de-Frida

## 1. Introduction

Dans ce lab, j’ai préparé un environnement complet pour utiliser **Frida** avec une application Android.  
L’objectif était d’installer les outils nécessaires côté ordinateur, de vérifier la connexion ADB avec l’émulateur Android, de récupérer la bonne version de `frida-server`, puis de valider la communication entre le client Frida sur Windows et le serveur Frida exécuté sur Android.

Ce lab représente une étape essentielle avant toute analyse dynamique Android, car Frida permet d’injecter des scripts dans une application afin d’observer son comportement à l’exécution.

--
## 2. Objectifs du lab

Les objectifs principaux étaient :

- vérifier l’installation de Python et pip ;
- installer le client Frida côté Windows ;
- vérifier que les commandes Frida fonctionnent ;
- vérifier l’installation d’ADB ;
- connecter un émulateur Android ;
- identifier l’architecture CPU de l’appareil Android ;
- télécharger la version compatible de `frida-server` ;
- transférer `frida-server` vers l’appareil Android ;
- valider la communication entre Frida et Android ;
- préparer l’environnement pour de futurs hooks Java ou natifs.

---

## 3. Environnement utilisé

| Élément | Description |
|---|---|
| Système | Windows |
| Terminal | PowerShell / Command Prompt |
| Appareil Android | Android Emulator |
| Architecture détectée | `x86_64` |
| Python | Python 3.14.3 |
| pip | pip 25.3 |
| ADB | Android Debug Bridge 1.0.41 |
| Frida | Frida 17.9.1 |
| Outils installés | `frida`, `frida-tools` |

---

## 4. Étape 1 — Vérification de Python et pip

### 4.1 Objectif

Avant d’installer Frida, j’ai vérifié que Python et pip étaient correctement installés sur Windows.  
Frida utilise Python pour installer le client et les outils en ligne de commande.

### 4.2 Commandes utilisées

Dans Command Prompt, j’ai exécuté :

```cmd
python --version
pip --version
```

### 4.3 Résultat obtenu

Le terminal affiche :

```text
Python 3.14.3
pip 25.3
```

Cela confirme que Python et pip sont installés et accessibles depuis le terminal.

<img width="693" height="454" alt="etp1p2" src="https://github.com/user-attachments/assets/1bcd7119-19f4-421e-89c1-1f8d6bcfb402" />


**Description de la capture :**  
Cette capture montre la vérification de Python et pip. Les deux commandes retournent une version valide, ce qui confirme que l’environnement Python est prêt pour l’installation de Frida.

---

## 5. Étape 2 — Installation du client Frida

### 5.1 Objectif

Le client Frida correspond aux outils installés sur l’ordinateur.  
Il permet de lancer des commandes comme :

```powershell
frida --version
frida-ps --help
frida -U
```

Ces commandes servent à communiquer avec un appareil Android ou un émulateur.

### 5.2 Commande utilisée

J’ai installé Frida et Frida Tools avec pip :

```powershell
pip install --upgrade frida frida-tools
```

ou avec l’interpréteur Python directement :

```powershell
python -m pip install --upgrade frida frida-tools
```

### 5.3 Résultat obtenu

L’installation télécharge et installe plusieurs paquets :

- `frida`
- `frida-tools`
- `colorama`
- `prompt-toolkit`
- `pygments`
- `websockets`
- `wcwidth`

À la fin, Frida est installé côté ordinateur.

<img width="668" height="394" alt="etp1p4" src="https://github.com/user-attachments/assets/f1eb1eaf-82a4-4a54-9661-f8e8712e22c9" />


**Description de la capture :**  
Cette capture montre l’installation de `frida` et `frida-tools` avec pip. On observe le téléchargement des dépendances, la création du paquet `frida-tools` et le message indiquant que les paquets ont été installés.

---

## 6. Étape 3 — Vérification de l’installation Frida

### 6.1 Objectif

Après l’installation, j’ai vérifié que Frida était bien installé et que les commandes principales fonctionnaient.

### 6.2 Commandes utilisées

```powershell
frida --version
frida-ps --help
python -c "import frida, sys; print('frida', frida.__version__)"
```

### 6.3 Résultat obtenu

Le terminal affiche :

```text
17.9.1
frida 17.9.1
```

Cela confirme que Frida est bien installé et que la bibliothèque Python Frida fonctionne correctement.

<img width="491" height="59" alt="etp3" src="https://github.com/user-attachments/assets/5ab2cbe6-5121-40a7-8e71-e2032b6d374e" />


**Description de la capture :**  
Cette capture montre la vérification de Frida avec `frida --version`, l’aide de `frida-ps` et la vérification de l’import Python avec `import frida`.

---

## 7. Étape 4 — Vérification d’ADB

### 7.1 Objectif

ADB est nécessaire pour communiquer avec l’émulateur ou le téléphone Android.  
Avant de transférer `frida-server`, il faut vérifier qu’ADB est installé et qu’il fonctionne.

### 7.2 Commandes utilisées

```powershell
adb version
adb devices
```

### 7.3 Résultat obtenu

La commande `adb version` affiche :

```text
Android Debug Bridge version 1.0.41
Version 36.0.2
```

ADB est installé dans le dossier :

```text
C:\Users\ROG STRIX\AppData\Local\Android\Sdk\platform-tools\adb.exe
```

La commande `adb devices` démarre le daemon ADB :

```text
daemon not running; starting now at tcp:5037
daemon started successfully
List of devices attached
```

Ce résultat confirme qu’ADB fonctionne.  
Si aucun appareil n’apparaît dans la liste, il faut démarrer l’émulateur ou connecter un téléphone avec le débogage USB activé.

<img width="581" height="237" alt="etp1p1" src="https://github.com/user-attachments/assets/640a41bb-fa25-4003-b8a3-cd490d3c8f8a" />

**Description de la capture :**  
Cette capture montre la vérification d’ADB avec `adb version` et `adb devices`. On voit que le daemon ADB démarre correctement, mais qu’aucun appareil n’est encore listé à ce moment-là.

---

## 8. Étape 5 — Vérification de l’architecture Android

### 8.1 Objectif

Avant de télécharger `frida-server`, il faut connaître l’architecture CPU de l’appareil Android.  
Cela permet de choisir la bonne version de `frida-server`.

### 8.2 Commande utilisée

```powershell
adb shell getprop ro.product.cpu.abi
```

### 8.3 Résultat obtenu

Le terminal affiche :

```text
x86_64
```

Cela signifie que l’émulateur utilise une architecture `x86_64`.  
Il faut donc télécharger une version de Frida Server compatible avec Android `x86_64`.

Exemple :

```text
frida-server-17.9.1-android-x86_64.xz
```

<img width="491" height="71" alt="etape3p1" src="https://github.com/user-attachments/assets/677a1aa6-291d-44d5-93e1-410aafad4452" />


**Description de la capture :**  
Cette capture montre la commande `adb shell getprop ro.product.cpu.abi` et le résultat `x86_64`, utilisé pour choisir la bonne version de `frida-server`.

---

<img width="704" height="49" alt="etp3p2" src="https://github.com/user-attachments/assets/06bce896-b473-462f-a947-f46676f8a176" />


**Description de la capture :**  
Cette capture confirme également que l’architecture de l’appareil Android est `x86_64`.

---

## 9. Étape 6 — Téléchargement et préparation de frida-server

### 9.1 Objectif

Après avoir identifié l’architecture `x86_64`, j’ai téléchargé la version compatible de `frida-server` depuis les releases officielles de Frida.

Le fichier à télécharger doit correspondre au format :

```text
frida-server-<version>-android-<architecture>.xz
```

Dans mon cas :

```text
frida-server-17.9.1-android-x86_64.xz
```

### 9.2 Décompression

Le fichier téléchargé est compressé au format `.xz`.

Sous Windows, j’ai utilisé **7-Zip** pour extraire le fichier.  
Après extraction, le fichier obtenu est renommé :

```text
frida-server
```

---

## 10. Étape 7 — Transfert de frida-server vers Android

### 10.1 Objectif

Une fois `frida-server` préparé, je l’ai copié vers l’émulateur Android dans le dossier :

```text
/data/local/tmp/
```

Ce dossier est souvent utilisé pour déposer des outils temporaires sur Android.

### 10.2 Commande utilisée

Depuis le dossier contenant `frida-server`, j’ai exécuté :

```powershell
adb push frida-server /data/local/tmp/
```

### 10.3 Résultat obtenu

Le terminal affiche :

```text
frida-server: 1 file pushed, 0 skipped.
```

Cela confirme que le fichier a été transféré correctement vers l’émulateur.

<img width="619" height="159" alt="wtp1p3" src="https://github.com/user-attachments/assets/ced02901-7d85-4630-978d-f3a7bc10b18a" />

**Description de la capture :**  
Cette capture montre le transfert de `frida-server` vers l’appareil Android avec la commande `adb push frida-server /data/local/tmp/`.

---

## 11. Étape 8 — Donner les droits d’exécution à frida-server

### 11.1 Objectif

Après le transfert, il faut rendre `frida-server` exécutable sur Android.

### 11.2 Commande utilisée

```powershell
adb shell chmod 755 /data/local/tmp/frida-server
```

Cette commande donne les permissions nécessaires pour lancer le fichier.

---

## 12. Étape 9 — Lancement de frida-server

### 12.1 Objectif

Le serveur Frida doit être lancé sur Android afin que le client Frida installé sur Windows puisse communiquer avec lui.

### 12.2 Commande simple

```powershell
adb shell /data/local/tmp/frida-server -l 0.0.0.0
```

Cette commande lance `frida-server` au premier plan.

### 12.3 Commande en arrière-plan

Il est aussi possible de le lancer en arrière-plan :

```powershell
adb shell "nohup /data/local/tmp/frida-server -l 0.0.0.0 >/dev/null 2>&1 &"
```

### 12.4 Méthode avec shell interactif

```powershell
adb shell
cd /data/local/tmp
./frida-server >/dev/null 2>&1 &
exit
```

---

## 13. Étape 10 — Vérification du lancement de frida-server

### 13.1 Objectif

Après le lancement, j’ai vérifié que le processus `frida-server` était bien actif sur Android.

### 13.2 Commandes possibles

```powershell
adb shell ps | findstr frida
```

ou :

```powershell
adb shell 
```
<img width="890" height="31" alt="image" src="https://github.com/user-attachments/assets/e3ac1352-e449-47f8-886e-1cd3851e256b" />

Si `frida-server` apparaît dans la liste, cela signifie qu’il fonctionne correctement sur l’appareil.

---

## 14. Étape 11 — Redirection des ports ADB

### 14.1 Objectif

Pour faciliter la communication entre le client Frida sur Windows et le serveur Frida sur Android, il est possible d’activer la redirection des ports.

### 14.2 Commandes utilisées

```powershell
adb forward tcp:27042 tcp:27042
adb forward tcp:27043 tcp:27043
```

Les ports `27042` et `27043` sont utilisés par Frida pour communiquer avec `frida-server`.

---

## 15. Étape 12 — Test de connexion Frida depuis Windows

### 15.1 Objectif

Une fois `frida-server` lancé, j’ai testé la connexion depuis Windows avec la commande :

```powershell
frida-ps -U
```

Cette commande liste les processus de l’appareil Android connecté.

### 15.2 Variante utile

Pour lister les applications installées :

```powershell
frida-ps -Uai
```

Si une liste de processus apparaît, cela confirme que :

- ADB fonctionne ;
- `frida-server` est actif ;
- Frida côté Windows communique avec Android.

---

## 16. Étape 13 — Test d’injection JavaScript simple

### 16.1 Objectif

Après avoir validé la connexion Frida, j’ai réalisé un test d’injection simple pour vérifier que Frida peut exécuter du JavaScript dans une application Android.

### 16.2 Création du fichier hello.js

J’ai créé un fichier nommé :

```text
hello.js
```

Contenu :

```javascript
Java.perform(function () {
  console.log("[+] Frida Java.perform OK");
});
```

### 16.3 Lancement du script

```powershell
frida -U -f com.example.app -l hello.js
```

Si l’application est lancée en mode suspendu, il faut reprendre l’exécution avec :

```text
%resume
```


### 16.4 Résultat attendu

```text
[+] Frida Java.perform OK
```

Ce résultat confirme que l’instrumentation Java fonctionne.

---

## 17. Étape 14 — Test de hook natif simple

### 17.1 Objectif

J’ai aussi préparé un test de hook natif pour vérifier que Frida peut intercepter une fonction native comme `recv`.

### 17.2 Création du fichier hello_native.js

```javascript
console.log("[+] Script chargé");

Interceptor.attach(Module.getExportByName(null, "recv"), {
  onEnter(args) {
    console.log("[+] recv appelée");
  }
});
```

### 17.3 Lancement du script

Pour s’attacher à un processus existant :

```powershell
frida -U -n "NomDuProcessus" -l hello_native.js
```

Ou pour lancer directement une application :

```powershell
frida -U -f com.example.app -l hello_native.js
```

### 17.4 Résultat attendu

```text
[+] Script chargé
[+] recv appelée
```

Le message `recv appelée` apparaît uniquement si l’application réalise une opération réseau utilisant cette fonction.

---

## 18. Étape 15 — Exploration de la console interactive Frida

Après l’injection d’un script, Frida ouvre une console interactive.  
Cette console permet d’exécuter des commandes JavaScript directement dans le processus cible.

Exemples de commandes utiles :

```javascript
Process.arch
Process.id
Process.platform
Process.mainModule
Java.available
Process.enumerateModules()
Process.enumerateThreads()
Process.enumerateRanges('r-x')
```

Ces commandes permettent de documenter le processus analysé et de mieux comprendre son environnement d’exécution.

---

## 19. Étape 16 — Observation de composants sensibles

Une fois Frida opérationnel, il devient possible d’observer plusieurs éléments importants dans une analyse Android :

- bibliothèques liées au chiffrement ;
- fonctions réseau ;
- accès au système de fichiers ;
- classes Java liées à la sécurité ;
- SharedPreferences ;
- SQLite ;
- vérifications de debug.

Exemples de fonctions natives intéressantes :

```javascript
connect
send
recv
open
read
```

Exemples de recherches Java utiles :

```javascript
Java.available
Java.enumerateLoadedClasses(...)
```

---

## 20. Résumé des commandes principales

| Action | Commande |
|---|---|
| Vérifier Python | `python --version` |
| Vérifier pip | `pip --version` |
| Installer Frida | `pip install --upgrade frida frida-tools` |
| Vérifier Frida | `frida --version` |
| Aide Frida | `frida-ps --help` |
| Vérifier ADB | `adb version` |
| Lister appareils | `adb devices` |
| Vérifier architecture | `adb shell getprop ro.product.cpu.abi` |
| Copier frida-server | `adb push frida-server /data/local/tmp/` |
| Donner permissions | `adb shell chmod 755 /data/local/tmp/frida-server` |
| Lancer frida-server | `adb shell /data/local/tmp/frida-server -l 0.0.0.0` |
| Forward ports | `adb forward tcp:27042 tcp:27042` |
| Lister processus Frida | `frida-ps -U` |

---

## 21. Liste des screenshots

| Screenshot | Description |
|---|---|
| `etp1p2.png` | Vérification de Python et pip |
| `etp1p4.png` | Installation de Frida et Frida Tools |
| `etp3.png` | Vérification de Frida avec `frida --version` et `frida-ps --help` |
| `etp1p1.png` | Vérification d’ADB avec `adb version` et `adb devices` |
| `etape3p1.png` | Vérification de l’architecture Android avec ADB |
| `etp3p2(1).png` | Confirmation de l’architecture `x86_64` |
| `wtp1p3.png` | Transfert de `frida-server` vers Android |
| `etape2.png` | Capture complémentaire liée à la préparation de l’environnement |

---

## 22. Difficultés rencontrées et solutions

### Aucun appareil détecté avec adb devices

Solution :

- démarrer l’émulateur Android ;
- vérifier que le débogage USB est activé ;
- relancer la commande :

```powershell
adb devices
```

---

### Frida est installé mais la commande n’est pas trouvée

Solution :

- vérifier que le dossier Scripts de Python est dans le PATH ;
- utiliser directement :

```powershell
python -m pip install --upgrade frida frida-tools
```

---

### Mauvaise version de frida-server

Solution :

- vérifier l’architecture avec :

```powershell
adb shell getprop ro.product.cpu.abi
```

- télécharger la version correspondante depuis les releases Frida.

---

### Permission refusée au lancement de frida-server

Solution :

```powershell
adb shell chmod 755 /data/local/tmp/frida-server
```

---

### frida-ps -U ne retourne rien

Solution :

- vérifier que `frida-server` est lancé ;
- vérifier la connexion ADB ;
- configurer les ports :

```powershell
adb forward tcp:27042 tcp:27042
adb forward tcp:27043 tcp:27043
```

---

## 23. Conclusion

Ce lab m’a permis de préparer un environnement fonctionnel pour l’analyse dynamique Android avec Frida.

J’ai vérifié Python, pip, ADB et Frida côté Windows, identifié l’architecture de l’émulateur Android, transféré `frida-server` vers l’appareil, puis préparé les premiers tests d’injection JavaScript.

À la fin de ce lab, l’environnement est prêt pour analyser une application Android à l’exécution, hooker des méthodes Java, observer des fonctions natives et documenter le comportement d’une application dans un contexte de sécurité mobile.
