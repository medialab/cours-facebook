# Comment collecter des données Facebook avec minet ?

Facebook est le troisième site web le plus visité au monde après Google et YouTube selon Alexa, et le plus grand réseau social avec plus de 2 milliards d'utilisateurs actifs. En France, Facebook a franchi la barre des 37 millions d'utilisateurs fin 2019 (source : [Wikipedia](https://fr.wikipedia.org/wiki/Facebook)). Facebook est donc un endroit intéressant pour récupérer des données.

Nous allons parler ici des différentes collectes de données Facebook qu'il est possible de réaliser à l'aide de la [librairie Python `minet`](https://github.com/medialab/minet) développée au médialab. Pour installer minet :

```
pip install minet
```

Les données Facebook peuvent être récoltées de deux manières : soit par l'API de CrowdTangle, soit en scrappant Facebook directement.

# 1. Récupérer des données Facebook par l'API CrowdTangle avec minet crowdtangle

CrowdTangle est un outil possédé et opéré par Facebook. Il permet d'accéder à des données Facebook, mais aussi Instagram et Twitter (on ne parlera que des données Facebook ici).

Sur Facebook, CrowdTangle récupère seulement des contenus publics (ni commentaires, ni données personnelles), venant de pages et de groupes publics (à l'exclusion donc des comptes personnels et des groupes privés). CrowdTangle donne accès aux données aggrégées d'interactions sur du contenu public, mais pas aux vues ou au reach de ce contenu.

Leur base de données n'inclut pas automatiquement tous les pages et groupes publics, mais seulement ceuw qui ont plus de 100,000 likes, ainsi que les comptes qui ont été ajoutés par les utilisateurs CrowdTangle dans leur dashboard.

Pour plus de renseignements sur l'API, [voici sa documentation officielle](https://github.com/CrowdTangle/API/wiki).

Pour afficher toutes les options possibles de minet relatives à CrowdTangle :
```
minet crowdtangle -h
```

# 2. Scrapper Facebook avec minet facebook

Scrapper une page consiste à BLABLABLA.

Pour afficher toutes les options possibles :

```
minet facebook -h
```

## 2.1 minet facebook comments

Pour récuperer tous les commentaires de certains posts Facebook :

```
minet fb comments https://www.facebook.com/nih.gov/posts/10158834154371830 > nih_post_comments.csv
minet fb comments url fb_url.csv -> url_comments.csv
```

## 2.2 minet facebook url-likes

Cette fonctionnalité permet de scrapper le bouton like de Facebook pour récupérer un nombre de likes approximatifs. Cela semble redondant avec minet ct link mais cela a certains avantages :
- donne un chiffre global sur toutes les pages, les groupes et les comptes publics comme privés, contrairement à CT

```
minet facebook url-likes https://www.lavoixdunord.fr/730139/article/2020-03-23/confinement-un-couple-verbalise-apres-avoir-promene-son-lapin-en-laisse
minet facebook url-likes url article_url.csv > article_likes.csv
```


# 3. Avantages et inconvénients des deux méthodes

API CrowdTangle :
- nécessite de demander un accès
- API documentée
- accès biaisé à Facebook (seulement comptes publics, et une recherche ne concerne que les comptes les plus gros et ceux ajoutés par les utilisateurs)
- l'API est lente, et a tendance à ne pas fonctionner certains jours

Scrapper Facebook :
- fonctionnalités limitées
- pas besoin de token
- accès à tout ce qui peut être scrappé (groupes privés, comptes personnels, contenu des commentaires Facebook)
- certaines collectes sont plus rapides que par l'API CrowdTangle