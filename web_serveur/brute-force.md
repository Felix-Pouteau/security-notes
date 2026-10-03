# Mot de passe faible (force brute / dictionnaire)

Un accès protégé accepte un mot de passe trop simple ou trop courant. Il
peut alors être retrouvé en essayant automatiquement de nombreux candidats :
une liste de mots courants (attaque par dictionnaire) ou toutes les
combinaisons possibles (force brute).

**Repérage :** un formulaire d'authentification qui ne limite ni le nombre
d'essais ni la vitesse des tentatives (pas de blocage de compte, pas de
CAPTCHA, pas de délai). Un nom d'utilisateur évident ou devinable (`admin`)
facilite l'attaque.

## Commandes

Tester d'abord à la main les grands classiques : `admin`, `password`,
`123456`, `root`, le nom du service.

Automatiser une attaque par dictionnaire sur un formulaire web (les `^USER^`
et `^PASS^` sont remplacés par chaque couple de la liste ; le dernier champ
est le message d'échec, qui indique à l'outil quand il a réussi) :
```bash
hydra -l admin -P liste.txt cible http-post-form \
  "/login:user=^USER^&pass=^PASS^:message d'echec"
```

## Défense

Imposer une politique de mots de passe robustes (longueur, complexité).
Limiter le nombre de tentatives (blocage temporaire ou CAPTCHA après
plusieurs échecs) pour rendre la force brute inefficace. Ajouter une
authentification à plusieurs facteurs pour qu'un mot de passe seul ne
suffise pas.

---

**Termes**

- **attaque par dictionnaire** : essayer une liste de mots de passe
  probables plutôt que toutes les combinaisons.
- **force brute** : essayer systématiquement toutes les combinaisons
  possibles d'un jeu de caractères.
- **rockyou.txt** : liste de mots de passe très répandue, souvent utilisée
  comme dictionnaire.
- **hydra** : outil d'automatisation des tentatives de connexion.
- **MFA (authentification à plusieurs facteurs)** : exige un second élément
  en plus du mot de passe.
