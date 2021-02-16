# Comment collecter des données Facebook avec minet ?

Facebook est le troisième site web le plus visité au monde après Google et YouTube selon Alexa, et le plus grand réseau social avec plus de 2 milliards d'utilisateurs actifs. En France, Facebook a franchi la barre des 37 millions d'utilisateurs fin 2019 (source : [Wikipedia](https://fr.wikipedia.org/wiki/Facebook)). Facebook est donc un endroit intéressant pour récupérer des données.

Nous allons parler ici des différentes collectes de données Facebook qu'il est possible de réaliser à l'aide de la [librairie Python `minet`](https://github.com/medialab/minet) développée au médialab. Pour installer minet :

```
pip install minet
```

Les données Facebook peuvent être récoltées de deux manières : soit par l'API de CrowdTangle, soit en scrappant Facebook directement.

# 1. Récupérer des données Facebook par l'API CrowdTangle avec minet crowdtangle

CrowdTangle est un outil possédé et opéré par Facebook. Il permet d'accéder à des données Facebook, mais aussi Instagram et Reddit (on ne parlera que des données Facebook ici). Pour plus de renseignements sur l'API, [voici sa documentation officielle](https://github.com/CrowdTangle/API/wiki). Voici les [guidelines pour citer CrowdTangle dans un article scientifique](https://help.crowdtangle.com/en/articles/3192685-citing-crowdtangle-data).

Sur Facebook, CrowdTangle récupère seulement des contenus publics (ni commentaires, ni données personnelles), venant de pages et de groupes publics (à l'exclusion donc des comptes personnels et des groupes privés). CrowdTangle donne accès aux données aggrégées d'interactions sur du contenu public, mais pas aux vues ou au reach de ce contenu.

Leur base de données n'inclut pas automatiquement tous les pages et groupes publics, mais seulement ceux qui ont plus de 100,000 likes, ainsi que les comptes qui ont été ajoutés par les utilisateurs CrowdTangle dans leur dashboard (plus d'explications [ici](https://help.crowdtangle.com/en/articles/1140930-what-data-is-crowdtangle-tracking) et [là](https://help.crowdtangle.com/en/articles/4558716-understanding-and-citing-crowdtangle-data)).

Pour pouvoir utiliser minet crowdtangle, il faut un token CrowdTangle à ajouter en paramètre des commandes ou à ajouter seulement une fois au fichier `.minetrc`. Le fichier `.minetrc.example` montre à quoi devrait ressembler un tel fichier.

Pour afficher toutes les options possibles de minet relatives à CrowdTangle :
```
minet crowdtangle -h
```

## 1.1. Les données d'output de CrowdTangle

Les données récupérées de `minet crowdtangle` sont toujours structurées de la même façon. Voir un exemple d'output [ici](https://github.com/medialab/cours-facebook/blob/main/output/example_posts.csv). 

Pour plus d'information sur les différentes colonnes voir la [documentation d'API à ce sujet](https://github.com/CrowdTangle/API/wiki/Post).

## 1.2. minet crowdtangle posts-by-id

Il est possible de récupérer ces données pour une liste d'URL de posts Facebook organisées dans un CSV :

```
minet ct posts-by-id url input/fb_url.csv > output/id_posts.csv
```

On voit que tous les posts ne sont pas forcément retrouvés, et que les URLs de photos et de vidéos ne sont pas retrouvées non plus.

Dans [la documentation](https://github.com/CrowdTangle/API/wiki/Posts#get-postid), on voit que l'id du post est nécessaire en paramètre de l'API. minet extrait donc l'id du post de son URL, avant de lancer l'appel à l'API pour récupérer les données, ce qui peut mal fonctionner selon l'URL.

## 1.3. minet crowdtangle posts

Cette commande permet de récupérer tous les posts publiés par un ensemble de pages ou de groupes publics Facebook. 

- Pour cela il faut d'abord créer une liste sur le dashboard CrowdTangle et y ajouter ces comptes. A noter que si on s'intéresse à des pages et des groupes, il faudra créer deux listes différentes (une pour les pages et une pour les groupes). 

- Ensuite cette commande nous permet de trouver l'id de la liste que nous venons de créer :
```
minet ct lists
```

- Enfin cet id doit être entré en paramètre de la commande pour collecter tous les posts de ces comptes :
```
minet ct posts --list-ids my_list_id --start-date 2021-01-01 > output/pages_posts.csv
```

On peut aussi ajouter une end-date à cette commande. Voir la [documentation](https://github.com/CrowdTangle/API/wiki/Posts) pour plus de détails sur cet endpoint.

## 1.4. minet crowdtangle search

Cette commande permet de chercher des posts dans la base de données CrowdTangle à partir de mots-clés.

```
minet ct search 'Your page has reduced distribution' --start-date 2021-01-01 --platform facebook > output/reduced_posts.csv
```

Comme l'endpoint search cherche des posts dans toutes les données CrowdTangle (Facebook, Instagram et Reddit), il faut préciser si l'on cherche seulement des données Facebook.

Par défaut, ces mots-clés sont cherchés dans le contenu textuel des posts et le contenu textuel des images, mais ils peuvent aussi être recherchés dans seulement un de ces champs seulement, dans le nom des comptes ou dans les URLs partagées dans les posts.

```
minet ct search 'Your page has reduced distribution' --start-date 2021-01-01 --platform facebook --search-field text_fields_only > output/reduced_posts_2.csv
```

Voir la [documentation](https://github.com/CrowdTangle/API/wiki/Search) pour plus de détails sur cet endpoint.

## 1.5. minet crowdtangle summary

Cette commande permet de récupérer le total des différentes réactions, commentaires et partages pour une liste d'URLs donnée :

```
minet ct summary url input/article_url.csv --start-date 2017-01-01 > output/article_summary.csv
```

Voir la [documentation](https://github.com/CrowdTangle/API/wiki/Links) pour plus de détails sur cet endpoint.

## 1.6. --resume, une option à connaître

L'option `--resume` permet de relancer une requête qui s'est interrompue. Par exemple on est en train de récupérer tous les posts d'une liste de pages avec cette commande :
```
minet ct posts --list-ids 1491244 --start-date 2015-01-01 > output/posts.csv
```

Si la commande s'interrompt on peut la redémarrer en indiquant le nom du fichier créé par la commande précédente :
```
minet ct posts --list-ids 1491244 --start-date 2015-01-01 --resume --output output/posts.csv
```

# 2. Scrapper Facebook avec minet facebook

Scrapper une page web consiste à rétro-engineerer le code HTML d'une page web dans le but d'extraire les données à l'intérieur de cette page web (prendre une page de Ouest France en exemple).

Pour afficher toutes les options possibles :
```
minet facebook -h
```

## 2.1. minet facebook comments

Cette commande permet de récupérer tous les commentaires d'un post Facebook :
```
minet fb comments https://www.facebook.com/groups/163572431960009/permalink/172068784443707/ > output/old_photo_post_comments.csv
```

Autre exemple :
```
minet fb comments https://www.facebook.com/welcome2parisguide/posts/2474939706136293 > output/paris_post_comments.csv
```

Attention, le temps de collecte est proportionnel au nombre de commentaires (1.5K commentaires sur cet exemple) :
```
minet fb comments https://www.facebook.com/nih.gov/posts/10158841949471830 > output/popular_post_comments.csv
```

Si on veut récuperer tous les commentaires d'une liste de posts, il faut créer un fichier CSV avec les URL de ces posts et lancer la commande :
```
minet fb comments url input/fb_url.csv > output/many_posts_comments.csv
```

## 2.2. minet facebook url-likes

Cette fonctionnalité permet de scrapper le bouton like de Facebook pour récupérer un nombre de likes reçus par une URL. Le bouton Like peut être ajouté à une page web, et permet aux visiteurs de cette page de liker ce contenu directement depuis le site web. Par exemple pour l'article `https://www.fredzone.org/oubli-mot-de-passe-bitcoin-545`, il est possible de trouver le nombre de personnes qui ont liké cet article en suivant ce lien : `https://www.facebook.com/plugins/like.php?href=https://www.fredzone.org/oubli-mot-de-passe-bitcoin-545`.

Ainsi cette commande minet peut récupérer le nombre de likes approximatif pour une URL donnée :

```
minet facebook url-likes https://www.fredzone.org/oubli-mot-de-passe-bitcoin-545 > output/bitcoin_article_likes.csv
```

On peut aussi récupérer le nombre de likes pour une liste d'URL si elles sont dans un fichier CSV :
```
minet facebook url-likes url input/article_url.csv > output/article_likes.csv
```

Cela a l'avantage d'aller 10 fois plus vite que l'endpoint `/link` de CrowdTangle, avec des données plus complètes (tous les comptes publics et privés sont comptés), mais on obtient des informations moins riches (seulement les likes), et approximatives.

## 2.3. minet facebook post-stats

On peut aussi scrapper Facebook pour récupérer des données liées à un post précis telles que le contenu du post, le nombre de commentaires, de partages, de réactions (like, love, wow, etc).

```
minet facebook post-stats url input/fb_url.csv > many_posts_stats.csv
```

# 3. Avantages et inconvénients des deux méthodes

Par l'API de CrowdTangle :

+ plutôt simple d'utilisation
+ API documentée
+ des métriques supplémentaires calculées par CrowdTangle

- nécessite de demander un accès
- accès biaisé à Facebook (seulement comptes publics, et une recherche ne concerne que les comptes les plus gros et ceux ajoutés par les utilisateurs)
- l'API est lente, et a tendance à ne pas fonctionner certains jours

En scrapant Facebook :
+ pas besoin de token
+ accès à tout ce qui peut être scrappé (groupes privés, comptes personnels, contenu des commentaires Facebook)
+ certaines collectes sont plus rapides que par l'API CrowdTangle
- des possibilités limitées
- les scripts se cassent facilement (à chaque évolution du code source de Facebook)
- certaines données sont approximatives (2.2K likes, 2 months ago, ...)
