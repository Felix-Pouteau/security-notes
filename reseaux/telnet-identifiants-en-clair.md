# Telnet : identifiants en clair

## Le concept

Telnet ouvre un terminal à distance **sans aucun chiffrement**. Tout ce qui est
tapé, identifiant et mot de passe compris, circule en clair sur le réseau.
Quiconque capture le trafic peut le relire.

## Repérage

- Port **23/TCP**.
- Dans une capture, **beaucoup de petits paquets** : Telnet envoie souvent
  un paquet par touche.
- Invites `login:` puis `Password:` envoyées par le serveur.
- L'identifiant apparaît en double (écho du serveur), le mot de passe une
  seule fois (pas d'écho, pour qu'il ne s'affiche pas à l'ecran).

## Commandes

```bash
# Ouvrir la capture dans Wireshark
wireshark capture.pcap &
```

```
telnet                    # filtre d'affichage : garde uniquement Telnet
ftp || telnet || http     # protocoles en clair, tous d'un coup
```

Clic droit sur un paquet → **Follow → TCP Stream** : recolle les paquets et
affiche la session comme un texte (rouge = client, bleu = serveur).

```bash
# Même chose en ligne de commande
tshark -r capture.pcap -Y telnet                  # liste les paquets Telnet
tshark -r capture.pcap -qz follow,tcp,ascii,0     # reconstitue le flux TCP n°0
```

Vue d'ensemble d'une capture inconnue : **Statistics → Protocol Hierarchy**.

## Défense

- Ne plus utiliser Telnet : le remplacer par **SSH** (chiffré).
- Désactiver le service et fermer le port 23 s'il n'est pas utile.
- Même logique pour FTP → SFTP / FTPS.

## Termes

- **Capture (.pcap)** : enregistrement du trafic réseau.
- **Flux TCP** : l'ensemble des paquets d'une même connexion.
- **Écho** : renvoi par le serveur de chaque caractère tapé, pour l'afficher.
- **En clair** : non chiffré, lisible tel quel.
