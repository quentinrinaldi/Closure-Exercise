# Exercice : Injection de dépendances et conteneur de services
​
## Présentation
​
Nous voulons mettre en un système de gestion des classes de l'application qui nous permette d'accéder aux objets dont nous avons besoin sans avoir à les instancier explicitement. Ce mécanisme est souvent appelé **conteneur de services**.

Un conteneur est une classe à laquelle on déclare les classes utilisées dans l'application. Le rôle du conteneur sera de délivrer àla demande des instences de ces classes **tout en injectant les dépendances**, c'est-à-dire les valeurs associée aux paramètres du constructeur de la classe.
​
Un conteneur simplifie donc l'architecture des applications en ce sens que nous n'avons pas nécessairment à connaître les dépendances d'une classe pour l'utiliser. Dans le cas d'applications volumineuses, ou de frameworks, cela permet de réutiliser des classes sans s'occuper de savoir comment elles fonctionnement. PAr ailleurs, cela évite un travail fastidieux de création des instances de ces classes et de création en chaîne d'instances de toutes les classes incluses.
​
Dans le cas général, un service est un singleton, c'est-à-dire que l'accès à un serevice renvoie toujours le même objet. Il est aussi utile, dans certains cas,de créer des services qui livrent des instnces muliples.
​
Un exemple simple de conteneur est la [bibliothèque Pimple](https://pimple.symfony.com/), un projet de Symfony. Cependant, [Pimple](https://github.com/silexphp/Pimple) ne s'occupe pas de l'injection des dépendances.

## Enoncé
​
### Le conteneur
​
Nous voulons créer une classe `Container` qui gérera les services. Le dictionnaire des services sera une proopriété de cette classes, sus forme de tableau ou d'une structure similaire. Eventuellement, la classe `Container` pourra implémenter l'interface `ArrayAccess` permettant d'utiliser l'objet comme un itérateur.
​
Le fonctionnement de cette classe, en prmier lieu est relativement simple et se compose de trois méthodes principales :
​
#### `register`
​
La méthode `register`permet de décarer un service au conteneur. Dans cette premièreversion, notre conteneur aura un comportement “vorace” (*eager*), c'est-à-dire qu'il crééra lors de l'enregistrement l'instance de la classe souhaitée. Cela nécéssitera bien sûr de veiller à la déclaration (préalable) des dépendances.
​
#### `factory`
​
La méthode `factory` permet de déclarer des services pour lesquelles chaque appel crée une instance nouvelle.  Dans ce cas de figure, l'instance ne devra pas être créée à l'enregistrement mais lors de l'accès. Cela induit que le comportement de ces services est “paresseux” (*lazy*), c'est-à-dire qu'ils ne sont pas créés tant que l'en n'en a pas besoin.
​
#### `get`
​
La méthode `get` pertmet d'accéder au service et de récupérer une instance de la classe complètement “configurée”, c'est-à-dire pourvude toutes ses dépendances.
​
​
### Utilisation
​
Une fois le conteneur créé, il faudra l'intégrer dans une application d'exemple, qui peut être un des exercices déjà réalisés comme la messagerie.
​
Le script principal décrira une méthode d'auto-chargement des classes, devra initialiser le conteneur puis exécuter l'application.
​
Il va de soi que les services ne sont pas exclusifs. Dans la plupart des cas, une application a recours *à la fois* à des services et à des objets classiques.
​
​
### Technique
​
Le point de l'exercice est l'utilisation des classes d'introspection de PHP. Nous avons vu, dans l'exercice sur la **currification** et le **pipelining**, l'utilisation de la classe `ReflectionFunction`.
​
Ici, nous allons appliquer la même technique mais en utilisant les classes `ReflectionClass` et `ReflectionMethod` (entre autres).
​
​
### Verions suivantes
​
On peut améliorer notre dispositifde plusieurs manières :
​
​
1. Dans la version initiale, les services sont déclarés “manuellement”. On souhairerait disposer d'une procédure qui initialise de conteneur, soit en découvrant les classes dans des répertoires, soit en lisant un fichier listant les classes à prendre en compte.
2. Pour le moment, nous n'avons considéré que des paramètres qui sont des objets. Mais les services ont quelquefois besoin de valeur *scalaires* (e.g. le nom d'un dossier du système de fichiers). On voudra donc mettre en œuvre une procédure qui décrive cette alternative de manière à ce que le conteneur sache comment faire lors de l'instanciation du service.
3. Dans cette lignée, nous avons vu qu'il est difficile de distinguer quelles dépendances concernent des services voraces ou des services paresseux. Nous aimerions pouvoir donner ce choix au développeur.
​
Pour ces améliorations, une des méthodes d'inspection intéressantes est `getDocComment`, qui permet de lire un bloc de commentaire au format **DocBlock** associé à une méthode ou à une classe. D'une manière générale, on écrira dans ce bloc des **annotations**, c'est-à-dire des chaînes de caractères d'un format défini, comme le fait **PHPDoc**.
 
## Réalisation de l'exercice

Je n'ai pas implémenter toutes les fonctionnalitées de l'énoncé. J'ai choisi de séparer mon conteneur standard au comportement "vorace", de mon conteneur factory au comportement "lazy".

Pour executer le code en version standard:
`php index.php`

Pour executer le code en version factory:
`php index.php factory`