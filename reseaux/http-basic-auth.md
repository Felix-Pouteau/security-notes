# HTTP Basic : Base64 n'est pas un chiffrement

## Le concept

L'authentification HTTP **Basic** envoie `identifiant:motdepasse` dans un
en-tête, simplement **encodé en Base64**. Base64 n'a ni clé ni secret : il se
décode instantanément. Sans HTTPS, c'est un mot de passe envoyé en clair.

## Repérage

- En-tête dans la requête HTTP :
  `Authorization: Basic <texte en Base64>`
- Base64 : lettres, chiffres, `+` et `/`, souvent terminé par `=` ou `==`.
- Une trame peut être fournie en **hexadécimal** (fichier texte) plutôt
  qu'en capture.

## Commandes

```bash
# Trame en hexa (texte) → octets réels → passages lisibles
xxd -r -p trame.txt | strings
```

- `xxd -r -p` : convertit l'écriture hexa (`48`) en vrai octet (`H`).
- `strings` : n'affiche que le texte lisible.

```bash
# Décoder du Base64 (exemple générique)
echo 'YWxpY2U6c2VjcmV0' | base64 -d     # → alice:secret
```

```bash
# Transformer la trame en .pcap pour l'ouvrir dans Wireshark
xxd -r -p trame.txt > trame.bin
od -Ax -tx1 -v trame.bin > dump.txt     # ajoute les décalages attendus
text2pcap dump.txt trame.pcap
```


## Défense

- Toujours **HTTPS** : le contenu, en-têtes compris, est chiffré.
- Ne pas utiliser Basic : lui préférer une authentification par formulaire ou par jeton.
- Ne jamais considérer un encodage comme une protection.

## Termes

- **Encodage** : changement de représentation, réversible sans clé.
- **Chiffrement** : rend illisible sans la clé.
- **Hexadécimal** : écriture d'un octet en deux caractères (`00` à `ff`).
- **Trame Ethernet** : unité de données de la couche 2, qui emballe IP, TCP
  puis le protocole applicatif.
