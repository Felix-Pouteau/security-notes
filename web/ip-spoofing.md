# Usurpation d'IP par en-tête HTTP

Certaines applications font confiance à un en-tête comme `X-Forwarded-For`
pour déterminer l'adresse du client. Or cet en-tête est fourni par le
client : il se falsifie.

**Repérage :** un accès conditionné à une IP « interne » ou privée
(plages `10.x`, `172.16–31.x`, `192.168.x` — RFC 1918).

## Commandes

Ajouter un en-tête forgé à une requête pour se faire passer pour un
client interne :
```bash
curl -H "X-Forwarded-For: 192.168.0.1" https://cible/ressource
```

Vérifier que l'en-tête forgé part bien (`-v` affiche la requête réellement
envoyée) :
```bash
curl -v -H "X-Forwarded-For: 192.168.0.1" https://cible/ressource
```

Voir sa propre IP publique — pour comprendre pourquoi, par défaut, le
serveur nous voit comme « externe » :
```bash
curl ifconfig.me
```

## Défense

Ne jamais fonder une décision de sécurité sur un en-tête modifiable par le
client. Se fier à l'IP vue au niveau TCP. En présence d'un proxy de
confiance, ne lire `X-Forwarded-For` que lorsqu'il provient de ce proxy.

---

**Termes**

- **en-tête HTTP** : ligne d'information envoyée avec une requête ou une
  réponse (ex. User-Agent, Host, X-Forwarded-For).
- **X-Forwarded-For** : en-tête censé transmettre l'IP réelle du client
  quand la requête passe par un proxy.
- **IP privée** : adresse réservée aux réseaux internes, non routée sur
  Internet (RFC 1918).
- **couche TCP** : niveau réseau où l'IP source est réellement observée,
  non modifiable par un simple en-tête.