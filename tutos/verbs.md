# Tutoriel : faire un test t (ou test de Student) avec R

## Étape 1 : chargement les données
Pour charger un fichier un .csv, il faut utiliser la fonction `read.csv()`, en indiquant à R soit :
- Le nom du fichier, si on est dans le bon répertoire de travail.
- Le chemin du fichier (son adresse), si on n'est pas dans le bon répertoire de travail.

Pour savoir dans quel répertoire de travail on est, il faut faire :
```
getwd()
[1] "/Users/Marie"
```

Si le fichier que vous souhaitez ouvrir (ici, verbs.csv) se trouve dans votre répertoire de travail, vous n'avez plus qu'à faire :

```
verbs <- read.csv("verbs.csv")
```

Sinon, vous avez deux solutions :
1. Changer de répertoire de travail. Vous pouvez le faire avec la fonction `setwd()`, en indiquant le chemin du dossier où se trouvent les données, ou cliquer sur *Session* puis *Set Working Directory* et enfin *Choose Directory* dans la barre d'outils de RStudio.
3. Indiquer le chemin de votre fichier, de la façon suivante (sur Mac) :

```
verbs <- read.csv("/Users/Marie/verbs.csv")
```

Sur Windows, le chemin ressemblera plutôt à ça :
```
verbs <- read.csv("C:\\Users\\Marie\\verbs.csv")
```

>  Comment trouver le chemin d'un fichier ou d'un dossier (répertoire) ?
> - Sur Mac : faites un clic droit (ou double tap) sur le nom du fichier, et appuyez touche "option" sans relâcher, choisir "copier XX en tant que nom de fichier.
> - Sur Windows :  clic droit sur le fichier, puis copier en tant que chemin d'accès

Maintenant qu'on a chargé notre fichier, on va l'inspecter. Tout d'abord avec la fonction `head()`, pour afficher les six premières lignes :
```
> head(verbs)
       verb past.tense regularity consistency imageability     RT Z.Score proportion.correct
1     arise      arose  irregular        0.50     4.451613 758.32   -0.02          0.7415730
2 backslide   backslid  irregular        1.00     3.696970 828.91    0.41          0.6477273
3    become     became  irregular        0.17     3.806452 773.91    0.16          0.9080460
4    befall     befell  irregular        0.13     2.100000 879.82    0.72          0.7386364
5     begin      began  irregular        0.08     3.258065 805.84    0.43          0.7045455
6    behold     beheld  irregular        0.40     3.333333 799.70    0.25          0.8505747
  proportion.reg.err
1         0.08988764
2         0.22727273
3         0.01149425
4         0.10227273
5         0.01136364
6         0.06896552
```

Et ensuite avec la fonction `str()` :
```
> str(verbs)
'data.frame':	344 obs. of  9 variables:
 $ verb              : chr  "arise" "backslide" "become" "befall" ...
 $ past.tense        : chr  "arose" "backslid" "became" "befell" ...
 $ regularity        : chr  "irregular" "irregular" "irregular" "irregular" ...
 $ consistency       : num  0.5 1 0.17 0.13 0.08 0.4 0.38 1 1 0.57 ...
 $ imageability      : num  4.45 3.7 3.81 2.1 3.26 ...
 $ RT                : num  758 829 774 880 806 ...
 $ Z.Score           : num  -0.02 0.41 0.16 0.72 0.43 0.25 0.62 0.85 0.88 0.7 ...
 $ proportion.correct: num  0.742 0.648 0.908 0.739 0.705 ...
 $ proportion.reg.err: num  0.0899 0.2273 0.0115 0.1023 0.0114 ...
 ```

On voit qu'on a neuf variables (les colonnes) et 344 observations (les lignes), dont trois variables composées de caractères (des mots), ou `chr`,  et six variables numériques, ou `num`.

## Étape 2 : formulation des hypothèses
### L'expérience
Le jeu de données utilisé ici est un extrait [du jeu de données](https://link.springer.com/article/10.3758/s13428-012-0240-y) qui accompagne l'article de Cohen-Shikora et al. (2013). Leur travail explore les processus cognitifs liés à la production du passé en anglais, par des locutrices et locuteurs natifs. Les chercheur.es ont demandé à 113 étudiantes et étudiants de l’université Washington de Saint-Louis, dans le Missouri, de donner la forme passé d'un verbe au présent le plus vite possible. On leur montrait un verbe, par exemple "play", et ils devaient donner le passé "played". Un dispositif mesurait le temps de réponse, c’est-à-dire le temps écoulé entre l’apparition du verbe sur l’écran et la réponse des participants. Les chercheur.es ont également comptabilité le nombre de bonnes réponses.

### Les hypothèses
On va se focaliser sur le temps de réponse, et se demander s'il y a un lien entre cette variable et le type de passé : régulier (walk &rarr; walked) et irrégulier (eat &rarr; ate). En se basant le bon sens (et sur nos souvenirs de l'apprentissage de l'anglais), on peut penser que le temps de réponse est plus long quand un verbe est irrégulier que quand il est régulier.

Avant tout test statistique, il faut formuler deux hypothèses : l'hypothèse alternative et l'hypothèse nulle. L'hypothèse alternative (H1), c'est votre hypothèse de recherche, celle qui vous intéresse. Ici :
-  **H1 : Le temps de réponse est plus long pour les verbes irréguliers**.

Maintenant on peut formuler l'hypothèse nulle (H0). Elle n’est pas « nulle » dans le sens de « mauvaise ». On dit qu’elle est « nulle » parce qu’elle postule qu’il n’y a pas de différence significative entre plusieurs groupes – ici, les verbes réguliers et les verbes irréguliers.
- **H0 : Il n'y a aucun lien entre le temps de réponse et la catégorie d'un verbe (irrégulier ou régulier)**.

## Étape 3 : préparation des données

Dans notre jeu de données, ce qui nous intéresse c'est `RT` (response time, ou temps de réponse) et `regularity` (régulier ou irrégulier). On va créer un sous-ensemble de données, juste pour y voir un peu plus clair, en l'appelant `x`, pour ne pas écraser le jeu de données complet :
```
> x <- verbs[, c("verb", "regularity", "RT")]
```

Voilà ce que ça donne :
```
> head(x)
       verb regularity     RT
1     arise  irregular 758.32
2 backslide  irregular 828.91
3    become  irregular 773.91
4    befall  irregular 879.82
5     begin  irregular 805.84
6    behold  irregular 799.70
```
On a vu plus haut que R considère `regularity` comme du texte. En fait, cette variable est un [facteur](https://www.youtube.com/watch?v=Nun9Hc2mBq0&t=18s), c'est-à-dire une variable catégorielle. On va donc modifier sa catégorie :

```
x$regularity <- as.factor(x$regularity)
```

On peut voir combien il y a de verbes réguliers ou irréguliers comme ça :
```
> table(x$regularity)

irregular   regular
      155       189
```

Pour la suite, on va créer deux data frames différents (ce n'est pas obligé, mais le code sera plus simple) :
```
reg <- x[x$regularity=="regular", ]
irreg <-x[x$regularity=="irregular", ]
```

## Étape 4 : exploration des données
On calcule la moyenne (mesure de tendance centrale) et l'écart type (mesure de dispersion) pour les verbes réguliers
```
> mean(reg$RT)
[1] 726.6546
> sd(reg$RT)
[1] 54.54879
```
La médiane et l'écart interquartile :
```
> median(reg$RT)
[1] 725.07
> IQR(reg$RT)
[1] 71.91
```
Maintenant, vous pouvez faire la même chose pour les verbes irréguliers :
```
> mean(irreg$RT) ; sd(irreg$RT)
[1] 780.9224
[1] 55.6997
```
Et :
```
> median(irreg$RT) ; IQR(irreg$RT)
[1] 776.64
[1] 87.09
```
On voit que, comme on s'y attend, le temps de réponse moyen et médian est plus élevé pour les verbes irréguliers que pour les verbes réguliers.

On peut aussi faire une boite à moustaches, cette fois-ci avec le jeu de données `x` (c'est plus simple). 
```
boxplot(x$RT~x$regularity, ylab = "temps de réponse en ms", xlab="catégorie")
```

On voit bien que les temps de réponses sont en général plus faibles pour les verbes réguliers. On a une valeur aberrante (le petit rond). Par curiosité, on peut regarder à quel verbe elle correspond. On commence par demander à la fonction `boxplot` de nous quelle est le temps de réponse anormalement élevé :
```
> boxplot(x$RT~x$regularity)$out
[1] 876.76
```
Et ensuite on l'affiche ainsi :
```
x[x$RT==876.76, ]
         verb regularity     RT
311 insinuate    regular 876.76
```
### Étape 5 : vérification des conditions du test t
Le test t (ou de Student) permet de comparer deux groupes. Il a plusieurs conditions :
- Les observations sont indépendantes (sinon, on fait un test t pour échantillons dépendants)
- Il y a au moins 30 observations par groupe ou les données ont une distribution normale.
- Les variances des échantillons sont homogènes (on s'en fiche un peu car si ce n'est pas le cas, R s'occupe de tout).

Ici, on a plus de 30 observations (155 et 189), donc c'est bon. On peut quand même vérifier que les données ne s'écartent pas trop de la [distribution normale](https://fr.wikipedia.org/wiki/Loi_normale), ce qui pourrait être problématique. On va faire des histogrammes :
```
hist(reg$RT)
```
```
hist(irreg$RT)
```

Le premier histogramme ressemble pas mal à une distribution en forme de cloche. Le second est plus irrégulier. On peut faire le test de Shapiro-Wilk pour voir si les distributions sont normales. Si la valeur p est supérieure à 0.05, on peut estimer que la distribution est normale. Pour les verbes réguliers, tout va bien :
```
> shapiro.test(reg$RT)

	Shapiro-Wilk normality test

data:  reg$RT
W = 0.99205, p-value = 0.3904
```
Pour les verbes irréguliers, la distribution ne suite pas la loi normale :
```
> shapiro.test(irreg$RT)

	Shapiro-Wilk normality test

data:  irreg$RT
W = 0.98167, p-value = 0.0372
```
Toutefois, comme il y a plus de 30 observations par groupe, ça ira.

## Étape 6 : le test t  
On utilise la fonction `t.test()` en indiquant nos deux groupes de temps de réponse. Comme on pense que le temps de réponse des verbes réguliers sera plus faible que celui des verbes réguliers, on utilise l'argument `alternative="less"`. Sinon, il faut écrire `"greater"` à la place de `less`.
```
> t.test(reg$RT, irreg$RT, alternative ="less")

	Welch Two Sample t-test

data:  reg$RT and irreg$RT
t = -9.075, df = 326.22, p-value < 2.2e-16
alternative hypothesis: true difference in means is less than 0
95 percent confidence interval:
      -Inf -44.40371
sample estimates:
mean of x mean of y 
 726.6546  780.9224
 ```
Ce qui nous intéresse, c'est la valeur p. Ici elle est si petite que R a décidé d'utiliser la notation scientifique au lieu de la notation décimale (= 0.00000000000000022). Comme la valeur p est inférieure à 0.05, on peut accepter l'hypothèse alternative : le temps de réponse est plus long pour les verbes irréguliers que pour les verbes réguliers.



> Comment rapporter les résultats ?
> 
> Indiquez la moyenne et l'écart type de chaque groupe ; indiquez également la statistique t (ici t = -9.075) et la valeur p. Vous pouvez l'arrondir à 0.001, on n'a pas besoin de tous les zéros.



## Référence
Cohen-Shikora, E. R., Balota, D. A., Kapuria, A., & Yap, M. J. (2013). The past tense inflection project (PTIP) : Speeded past tense inflections, imageability ratings, and past tense consistency measures for 2,200 verbs. *Behavior research methods*, *45*, 151‑159.
