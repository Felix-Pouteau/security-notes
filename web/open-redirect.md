# Open redirect

Une application redirige vers une URL fournie par l'utilisateur sans vérifier
qu'elle fait partie des destinations autorisées.

**Repérage :** un paramètre d'URL contenant une adresse (`?url=`, `?redirect=`,
`?next=`). Piège courant : une signature qui ne dépend pas d'un secret serveur
peut être recalculée, donc ne protège rien.

## Commandes

Calculer l'empreinte MD5 d'une valeur (utile quand une « signature » n'est
qu'un hash de l'URL) :
```bash
echo -n "https://exemple.com" | md5sum
```

Lire la réponse sans suivre la redirection, pour voir le code HTTP (301/302)
et l'en-tête `Location` (`-i` affiche les en-têtes) :
```bash
curl -i "https://cible/endpoint?url=https://exemple.com"
```

## Défense

Valider la destination contre une liste blanche de domaines / chemins
autorisés. Ne jamais rediriger vers une valeur fournie par le client sans
contrôle. Si une signature est utilisée, la baser sur un secret serveur (HMAC),
jamais sur un simple hash de l'entrée.

---

**Termes**

- **redirection** : réponse HTTP (code 301/302) qui envoie le navigateur vers
  une autre adresse, indiquée dans l'en-tête `Location`.
- **hash** : empreinte de taille fixe calculée à partir d'une donnée ; même
  entrée = même empreinte (ex. MD5).
- **HMAC** : signature calculée à partir d'une donnée **et** d'un secret ;
  impossible à reproduire sans connaître le secret.
- **liste blanche** : ensemble fermé de valeurs autorisées ; tout ce qui n'y
  figure pas est refusé.