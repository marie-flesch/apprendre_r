# Comment créer un échantillon aléatoire avec R ? 

Composé de deux parties, ce tutoriel vidéo explique comment créer un échantillon aléatoire (ou tirage au sort) avec R. 
La première partie montre comment créer un échantillon à partir d'un vecteur, et la seconde à partir d'un *data frame*. 

## Partie 1
J'ai un vecteur qui contient les noms de 20 villes françaises : 

 ```villes <- c("Paris", "Marseille", "Lyon", "Toulouse", "Nice", "Nantes", "Strasbourg", "Montpellier", "Bordeaux", "Lille", "Rennes", "Reims", "Le Havre", "Saint-Étienne", "Toulon", "Grenoble", "Dijon", "Nîmes", "Angers", "Villeurbanne")```

Pour créer un échantillon, j'utilise la fonction `sample`. Elle prend deux arguments : 1. le nom du vecteur, et 2. le nombre d'éléments que je souhaite tirer au sort. 

```sample(villes, 1)```

J'obtiens (vous obtiendrez sans doute quelque chose de différent)

... et la gagnante est donc, roulement de tambour... : "Nîmes" !

Et si je veux 5 villes par exemple, je remplace le 1 par un 5. 
Pour créer

sample(villes, 1)
sample(villes, 5)


sample(140, 10)


## Parrtie 2
