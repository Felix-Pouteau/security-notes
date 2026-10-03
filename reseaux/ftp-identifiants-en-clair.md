# FTP : identifiants en clair

## Le concept

FTP transfère des fichiers **sans chiffrement**. À la connexion, l'identifiant
et le mot de passe transitent en clair dans les commandes `USER` et `PASS`.
Quiconque capture le trafic peut les lire.

## Repérage

- Port **21/TCP** (canal de commande).
- Dans la colonne Info : commandes client en majuscules (`USER`, `PASS`,
  `LIST`...) et réponses serveur à 3 chiffres (`220`, `331`, `230`).
- Le couple à trouver : la valeur après `USER` et celle après `PASS`.

## Commandes

```bash
# Ouvrir la capture dans Wireshark
wireshark capture.pcap &
```

```
ftp                       # filtre d'affichage : garde uniquement FTP
```

Lire la colonne Info, ou clic droit sur un paquet → **Follow → TCP Stream**
pour voir tout l'échange d'un coup.

```bash
# En ligne de commande
tshark -r capture.pcap -Y ftp                              # paquets FTP
tshark -r capture.pcap -Y 'ftp.request.command=="PASS"' \
  -T fields -e ftp.request.arg                             # isole le mot de passe
```

Vue d'ensemble d'une capture inconnue : **Statistics → Protocol Hierarchy**.

## Défense

- Remplacer FTP par **SFTP** (sur SSH) ou **FTPS** (sur TLS), chiffrés.
- Désactiver le service et fermer le port 21 s'il n'est pas utile.
- Ne jamais réutiliser ailleurs un mot de passe passé en clair.

## Termes

- **Capture (.pcap)** : enregistrement du trafic réseau.
- **Canal de commande** : connexion FTP qui transporte les ordres (port 21),
  distincte du canal de données qui transporte les fichiers.
- **Flux TCP** : l'ensemble des paquets d'une même connexion.
- **En clair** : non chiffré, lisible tel quel.