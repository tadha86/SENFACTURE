# Obtenir l'installateur Windows et l'application Android

Vous n'avez rien à installer sur votre ordinateur : GitHub construit les deux fichiers gratuitement.

## Méthode simple (sans programmer)
1. Créez un compte gratuit sur github.com, puis un **nouveau dépôt privé** (bouton « New »).
2. Dézippez `caisse-stock-complet.zip` et envoyez tout le contenu du dossier `caisse-serveur` dans ce dépôt (« Add file » > « Upload files »). Le dossier caché `.github` doit être envoyé aussi.
3. Si vous avez déjà généré vos clés de licence (voir le guide principal), ajoutez `license-public.pem` à la racine du dépôt **avant** de construire. Ne mettez jamais `vendor-private.pem` sur GitHub.
4. Onglet **Actions** > « Construire Windows et Android » > **Run workflow**.
5. Après 10 à 15 minutes, ouvrez l'exécution terminée : en bas, section **Artifacts** :
   - `caisse-windows` : installateur `Caisse & Stock Setup.exe` (à installer sur les PC Windows 10 et 11)
   - `caisse-android` : fichier `.apk` (à copier sur le téléphone ou la tablette, puis l'ouvrir ; Android demandera d'autoriser l'installation depuis cette source)

## Utilisation
- **Windows** : à l'installation, Windows peut afficher « Windows a protégé votre ordinateur » (l'application n'est pas encore signée) : cliquez sur « Informations complémentaires » puis « Exécuter quand même ». Un certificat de signature de code (payant) supprime cet avertissement pour vos clients.
- **PC serveur du magasin** : menu **Réseau** > « Autoriser les autres appareils du réseau local ». Windows demandera d'autoriser le pare-feu : acceptez pour les réseaux privés.
- **Android** : à la première ouverture, saisissez l'adresse du serveur (par exemple `http://192.168.1.10:3000`, l'adresse du PC serveur, ou l'adresse HTTPS de votre serveur cloud).

## Avant de vendre
- Dans `mobile/capacitor.config.json` et `desktop/package.json`, remplacez l'identifiant `com.example.caissestock` par le vôtre (par exemple `sn.votreentreprise.caisse`).
- L'APK fourni est un **APK de test** (signature de débogage) : parfait pour installer chez vos clients à la main. Pour le Google Play Store, il faut un fichier signé avec votre propre clé : demandez-moi d'ajouter cette étape.
- Testez sur de vrais appareils : ces constructions n'ont pas pu être exécutées dans mon environnement, seulement préparées.
