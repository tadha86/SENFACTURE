# Caisse & Stock : logiciel complet de facturation et de gestion de stock

Pour le Sénégal (FCFA, TVA 18 %) et la Gambie (dalasi, TVA 15 %). Une seule base de code :

| Dossier | Rôle |
|---|---|
| `src/` | Serveur (API, base SQLite, rôles, caisses, ventes, rapports, licences), sans dépendance, Node.js 22.5+ |
| `public/` | Interface web : caisse, stock, clients, ventes, rapports, équipe, réglages. Ordinateur, tablette et téléphone |
| `desktop/` | Application Windows et Mac (Electron) avec serveur intégré |
| `mobile/` | Applications Android et iPhone (Capacitor) qui se connectent au serveur |
| `tools/generateur-licences.html` | Outil du vendeur : page à ouvrir d’un double-clic pour créer vos clés et les clés d’activation (`license-tool.js` fait la même chose en ligne de commande) |
| `test/` | Tests automatiques (`npm test`) |

## Démarrage rapide
```bash
npm start                    # http://127.0.0.1:3000 : première configuration au premier lancement
HOST=0.0.0.0 npm start       # réseau local : les autres appareils ouvrent http://IP-DU-PC:3000
```
Variables : `PORT`, `HOST`, `DB_PATH` (défaut `./data.sqlite`), `APP_SECRET`, `CORS_ORIGIN`, `LICENSE_PUBLIC_KEY_FILE`.

## Les trois modes
- **Monoposte** : application de bureau (`desktop/`) ou `npm start` sur le PC de la caisse. Aucune connexion nécessaire.
- **Réseau local** : un PC fait office de serveur ; caisses, tablettes et téléphones s'y connectent en Wi-Fi. Fonctionne sans internet.
- **Cloud** : le même serveur sur un hébergement, derrière HTTPS (Caddy ou nginx), avec `APP_SECRET` fixé et `CORS_ORIGIN` limité. Les applications mobiles se connectent à son adresse.

## Hors-ligne
Si le serveur est injoignable, l'interface garde les produits en mémoire et enregistre les ventes sur l'appareil ; elles sont envoyées dès le retour du réseau. Chaque vente a un identifiant unique : un renvoi ne crée jamais de doublon. Le numéro de facture définitif est attribué à la synchronisation (le ticket hors-ligne le mentionne).

## Rôles
Propriétaire (tout), gérant (stock, remises, retours, rapports, caisses), caissier (vente, caisse, clients), magasinier (produits et stock). Les droits sont vérifiés par le serveur, pas seulement par l'écran.

## Licences (vente du logiciel)
1. Une seule fois, chez vous : `node tools/license-tool.js genkeys` crée `vendor-private.pem` (**secret, ne jamais distribuer**) et `license-public.pem`.
2. Placez `license-public.pem` à côté du serveur (ou dans `desktop/` avant `npm run dist`) : les licences sont alors vérifiées.
3. Pour chaque client : `node tools/license-tool.js issue --customer "Boutique Awa" --type annual --days 365 --max-shops 2` (ou `--type lifetime`).
4. Le client colle la clé dans Réglages > Licence.

Sans clé : essai de 30 jours. Licence annuelle expirée ou essai terminé : le logiciel passe en **lecture seule** (consultation, rapports et exports restent possibles, on ne perd jamais l'accès à ses données). Sans `license-public.pem`, le serveur est en mode développement : tout est permis.

## Créer les applications
**Windows et Mac** (sur une machine avec internet) :
```bash
cd desktop && npm install && npm run dist     # installateur .exe (sur Windows) ou .dmg (sur Mac) dans desktop/dist
```
Un installateur Mac se construit sur un Mac ; un installateur Windows, sur Windows.

**Android et iPhone** :
```bash
cd mobile && npm install && npx cap add android && npx cap add ios && npx cap sync
npx cap open android      # Android Studio : Build > Generate Signed Bundle (publication Play Store)
npx cap open ios          # Xcode (sur Mac) : Product > Archive (publication App Store)
```
Au premier lancement, l'application demande l'adresse du serveur (`http://192.168.1.10:3000` en réseau local, ou l'adresse HTTPS du cloud). Pour un serveur en HTTP sur le réseau local, iOS exige une exception dans `Info.plist` (`NSAppTransportSecurity` > `NSAllowsLocalNetworking`). Pour le cloud, utilisez HTTPS.

## Impression
Ticket et facture s'impriment avec le bouton Imprimer du navigateur ou de l'application : choisissez l'imprimante (A4 ou ticket 58/80 mm) ou « Enregistrer en PDF ». Une imprimante thermique en ESC/POS direct n'est pas incluse.

## Sécurité et sauvegarde
Mots de passe hachés (scrypt), jetons signés de 12 h, blocage après 5 essais, journal d'audit. Le serveur n'a pas de HTTPS intégré : en cloud, placez-le derrière un proxy HTTPS. **Sauvegardez `data.sqlite`** (copie du fichier serveur arrêté, ou `sqlite3 data.sqlite ".backup sauvegarde.sqlite"`) régulièrement.

## Limites connues
- Les applications de bureau et mobiles (`desktop/`, `mobile/`) sont configurées mais n'ont pas été compilées ni testées sur de vrais appareils ; le serveur et l'interface web sont testés (tests automatiques et parcours dans un navigateur).
- Les encaissements de dettes clients ne sont pas rattachés à une session de caisse.
- Wave et Orange Money sont enregistrés comme modes de paiement, sans appel à leurs API.
- Pas de transmission automatique des factures aux administrations fiscales (DGID au Sénégal, GRA en Gambie) : à vérifier avec elles avant la vente.
- Une seule devise par boutique, interface en français uniquement.
