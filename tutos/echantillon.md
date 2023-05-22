# Comment créer un échantillon aléatoire avec R ? 

Composé de deux parties, ce tutoriel vidéo explique comment créer un échantillon aléatoire (ou tirage au sort) avec R. 
[La première partie](https://www.youtube.com/watch?v=Gv-5cU6HMhw) montre comment créer un échantillon à partir d'un vecteur, et la seconde à partir d'un *data frame*. 

## Partie 1
J'ai un vecteur qui contient les noms de 20 villes françaises&nbsp;: 

 ```
 villes <- c("Paris", "Marseille", "Lyon", "Toulouse", "Nice", "Nantes", "Strasbourg", "Montpellier", "Bordeaux", "Lille", "Rennes", "Reims", "Le Havre", "Saint-Étienne", "Toulon", "Grenoble", "Dijon", "Nîmes", "Angers", "Villeurbanne")
 ```

Pour créer un échantillon, j'utilise la fonction `sample()`. Elle prend deux arguments&nbsp;: 1. le nom du vecteur, et 2. le nombre d'éléments que je souhaite tirer au sort. 

```
sample(villes, 1)
```

J'obtiens (vous obtiendrez très certainement quelque chose de différent) :

```
[1] "Nîmes"
```

Et si je veux 5 villes par exemple, je remplace le 1 par un 5&nbsp;: 

```
sample(villes, 5)
[1] "Reims"    "Grenoble" "Paris"    "Nantes"   "Nîmes"
```

La fonction `sample() peut également prendre comme premier argument un nombre. Dans ce cas, elle tire au sort un ou ou plusieurs nombres. Ici, je veux 10 nombres compris entre 0 et 140&nbsp;: 

```
sample(140, 10)
[1] 116  15  77  22 124 140  73  24 117   4
```

Voilà, donc, quand on a un vecteur, c'est assez simple. Mais qu'est-ce qui se passe quand on veut réaliser un échantillon à partir d'un data frame, ou tableau de données, c'est-à-dire tirer au sort un certain nombre de lignes ? C'est un peu différent&nbsp;!

## Partie 2
Je vais vous montrer deux méthodes : la première utilise des fonctions de base de R, et la seconde fait appel au package dplyr. 

### Avec les fonctions de base
On va utiliser un jeu de données disponible sur le site de l'INSEE (que vous pouvez télécharger [ici](https://github.com/marie-flesch/apprendre_r/blob/main/data/population_communes_france.csv)) qui indique la population de chaque commune française. 

Je le charge dans R, en lui donnant le nom `pop` et je l'inspecte rapidement : 
```pop <- read.csv("population_communes_france.csv")
head(pop)
> head(pop)
                  commune population               région
1 L'Abergement-Clémenciat        821 Auvergne-Rhône-Alpes
2   L'Abergement-de-Varey        268 Auvergne-Rhône-Alpes
3       Ambérieu-en-Bugey      14662 Auvergne-Rhône-Alpes
4     Ambérieux-en-Dombes       1806 Auvergne-Rhône-Alpes
5                 Ambléon        113 Auvergne-Rhône-Alpes
6                Ambronay       2946 Auvergne-Rhône-Alpes
```

voit qu'il y a le nom de chaque commune, sa population, et la région dans laquelle elle est située.

Je peux tout à fait utiliser la fonction `sample()` comme nous l'avons vu avant, pour obtenir un échantillon aléatoire de 15 communes, en indiquant le nom de la colonne, donc un vecteur, dans ma fonction.

```
> sample(pop$commune, 15)
 [1] "Champigneulles-en-Bassigny" "Chalivoy-Milon"            
 [3] "Saint-Cyr-l'École"          "Carlux"                    
 [5] "Pécy"                       "Vuillery"                  
 [7] "Bouisse"                    "Montgesty"                 
 [9] "Bonnieux"                   "Nitry"                     
[11] "Neuillé"                    "Bandol"                    
[13] "Venelles"                   "Bennwihr"                  
[15] "Vasperviller"    
```

Mais je ne peux pas mettre un data frame à l'intérieur de la fonction `sample()`, puisque ce qu'elle veut elle c'est soit un vecteur ou un nombre - et là, je vais lui donner un nombre. 

Et ce nombre, c'est quoi ? Et bien, ça va être le nombre de lignes qu'il y a dans mon data frame. 

On va donc prélever un échantillon d'indices, c'est à dire de numéros de ligne, puis utiliser cet échantillon de numéros pour créer un sous-ensemble de données, c'est à dire un échantillon de mon dataframe avec toutes les colonnes. 

Alors, pour comprendre la marche à suivre, un petit rappel. 

Vous savez que pour extraire des lignes spécifiques d'un data frame, il faut tout d'abord indiquer le nom du data frame, puis indiquer l'indice des lignes entre crochets, suivi d'une virgule et de rien, puisqu'on veut toutes les colonnes. 

Par exemple, pour avoir la première ligne de `pop`, je met entre crochets 1 virgule rien, puisque je veux toutes les colonnes :
```
> pop[1, ]
                  commune population               région
1 L'Abergement-Clémenciat        821 Auvergne-Rhône-Alpes
```
Et pour avoir la millième ligne, je fais : 
```
> pop[1000, ]
                 commune population          région
1000 Quincy-sous-le-Mont         63 Hauts-de-France
```
Pour tirer des lignes au sort, on va utiliser le même principe, sauf que, ben, comme on veut des lignes tirées au sort, on va utiliser la fonction `sample()` à la place de numéros de ligne précis. On va donc enchâsser la fonction `sample()` entre les crochets. Et on va lui donner le nombre de lignes qu'il y a dans le dataframe, ici 34980, et évidemment la taille de l'échantillon désiré. Donc en gros, on dit à la fonction&nbsp;: &laquo;&nbsp;j'ai 34980 lignes dans mon dataframe, tires-en 5 au sort&nbsp;&raquo;. 
```
> pop[sample(34980, 1), ]
      commune population               région
2362 Vinzieux        486 Auvergne-Rhône-Alpes
``` 
Alors, comment je savais qu'il y a 34980 lignes dans le dataframe ? Et bien j'ai utilisé la fonction `nrow()`, qui renvoie le nombre de lignes dans un dataframe : 
```
> nrow(pop)
[1] 34980
```
On peut aussi enchâsser cette fonction dans la fonction `sample()`, de cette façon, ici pour avoir un échantillon de cinq lignes : 
```
> pop[sample(nrow(pop), 5), ] 
           commune population           région
28547     Maresché        896 Pays de la Loire
18626 Saint-Memmie       5523        Grand Est
19937   Sornéville        311        Grand Est
17906    Le Loreur        282        Normandie
20692     Plumelin       2823         Bretagne
```

Et évidemment, si je veux garder une trace de cet échantillon, il faut que je lui donne un petit nom : 
```
> mon_echantillon <- pop[sample(nrow(pop), 5), ]
```

Phew! C'était un petit peu compliqué, j'avoue, et il y a une méthode plus simple, qui utilise le package dplyr()

### Avec le package `dplyr`

Si vous n'avez pas déjà installé le package, faites-le : 
```
> install.packages("dplry")
```

On charge le package en utilisant la fonction `library()` :
```
> library(dplyr)
```

Et on utilise la fonction `slice_sample`, qui est toute simple. Elle prend comme arguments le nom de notre data frame, et le nombre de lignes qu'on veut. 
```
> slice_sample(pop, n=5)
                commune population                  région
1        Pont-sur-Vanne        191 Bourgogne-Franche-Comté
2              Champlin         41 Bourgogne-Franche-Comté
3 Bettegney-Saint-Brice        161               Grand Est
4        Castéra-Loubix         52      Nouvelle-Aquitaine
5              Louvigny        176        Pays de la Loire
```

Et ce qui est super, c'est qu'on peut aussi utiliser cette fonction pour réaliser un échantillon par groupe. Par exemple, on peut imaginer que je souhaite tirer une commune au sort par région. Il faut faire comme ça : 
```
> slice_sample(group_by(pop, région), n=1)
# A tibble: 17 × 3
# Groups:   région [17]
   commune                 population région                    
   <chr>                        <int> <chr>                     
 1 Corenc                        4232 Auvergne-Rhône-Alpes      
 2 Reculfoz                        41 Bourgogne-Franche-Comté   
 3 Tréméreuc                      753 Bretagne                  
 4 Lublé                          141 Centre-Val de Loire    
 ...
 ```

On peut aussi écrire, en utilisant l'opérateur *pipe* propre à `dplyr` : 
```
pop %>% group_by(région) %>% slice_sample(n=1)
```

