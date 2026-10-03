# Injection de commande OS

Une application passe une entrée utilisateur à une commande système (shell)
sans la filtrer. L'attaquant enchaîne alors sa propre commande à celle
prévue et la fait exécuter par le serveur.

**Repérage :** une fonctionnalité qui lance une commande système à partir
d'une entrée (ping, nslookup, conversion de fichier, envoi de mail...). Le
code source, s'il est accessible, montre un appel du type `shell_exec`,
`system` ou `exec` avec une variable utilisateur concaténée.

## Commandes


```bash
127.0.0.1 ; cat fichier
127.0.0.1 && cat fichier
```

Lire un fichier source (ex. .php) sans qu'il soit interprété par le
navigateur : l'encoder en base64 côté serveur, puis le décoder en local :
```bash
127.0.0.1 ; cat index.php | base64
echo "<chaîne_base64>" | base64 -d
```

Localiser un fichier dont on ignore le chemin :
```bash
127.0.0.1 ; find / -name .passwd 2>/dev/null
```

## Défense

- Ne jamais passer une entrée utilisateur à un shell. 
- Utiliser des API qui séparent la commande de ses arguments plutôt que de concaténer une chaîne.
- Valider strictement l'entrée contre une liste blanche (par exemple, une
expression régulière n'acceptant qu'une adresse IP). 
- Appliquer le moindre privilège pour limiter ce que le service peut lire ou exécuter.

---

**Termes**

- **shell** : interpréteur de commandes système (bash, sh...).
- **shell_exec / system / exec** : fonctions qui exécutent une commande
  système depuis un langage applicatif (PHP ici).
- **chaînage** : enchaîner plusieurs commandes sur une même ligne avec
  `;`, `&&`, `|` ou `$( )`.
- **base64** : encodage qui transforme des données en texte neutre, non
  interprété par le navigateur ; se décode avec `base64 -d`.
- **RCE (Remote Code Execution)** : exécution de code arbitraire à distance,
  la conséquence la plus grave de cette faille.
