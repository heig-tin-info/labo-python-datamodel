# Datamodel Python

Durant ce cours, nous avons vu les bases du langage Python, durant ces 16 semaines nous avons abordé l'écosystème de Python, ses modules, ses librairies, ses outils, ses bonnes pratiques, etc.

Pour parfaire vos connaissances, je vous propose de vous plonger dans la documentation officielle de Python, qui est une mine d'or pour tout développeur Python.

Le *Data model* est une partie de la documentation qui est très intéressante à lire. Elle n'est pas très digeste pour un débutant, mais adopter le reflexe de s'y référer est une bonne habitude à prendre.

Pour ce travail pratique, nous allons plonger dans le *Data model* de Python accessible [ici](https://docs.python.org/3/reference/datamodel.html).

## Types particuliers

### None

Le type [None](https://docs.python.org/3/reference/datamodel.html#none) est une instance unique de `NoneType` qui est souvent utilisé pour représenter l'absence de valeur. Il est retourné par des fonctions qui ne retournent pas explicitement de valeur. C'est la valeur par défaut de retour des fonctions:

```python
def foo():
    pass

u = foo()
```

Quel est le type de `u` ?

> [!TIP]
> Pour afficher le type d'une variable, vous pouvez utiliser la fonction `type()`.

Vous allez obtenir la valeur `NoneType` qui est la classe de l'objet `None`. Comme pour tous les objets Python, le type d'une classe, y-compris `NoneType`, est `type`

```python
>>> type(type(None))
type
```

### Ellipse

L'objet [Ellipse](https://docs.python.org/3/reference/datamodel.html#Ellipsis) est une instance unique de la classe `ellipsis`. Il est utilisé pour indiquer une opération en cours ou une valeur par défaut dans les slices.

Sa représentation littérale est `...`.

On peut l'utiliser dans plusieurs cas de figure:

L'`Ellipsis` en Python est représenté par les trois points de suspension (`...`). Bien que son utilisation ne soit pas très courante, il existe plusieurs cas où l'`Ellipsis` peut être utile :

1. **Indicateur de code incomplet ou à compléter plus tard :**
   L'`Ellipsis` peut être utilisé comme un espace réservé pour indiquer que du code doit être ajouté à cet endroit ultérieurement. C'est souvent utilisé lors du développement ou du prototypage rapide.

   ```python
   def function_to_be_implemented_later():
       ...
   ```

2. **Slicing avancé avec Numpy :**
   L'`Ellipsis` est particulièrement utile avec les bibliothèques comme NumPy pour représenter plusieurs tranches. Il peut être utilisé pour sélectionner des sous-ensembles de tableaux de manière concise.

   ```python
   import numpy as np
   array = np.random.rand(4, 3, 2)
   print(array[..., 1])  # Sélectionne la dernière dimension
   ```

3. **Méthodes et classes abstraites :**
   Lors de la définition de classes et de méthodes abstraites, l'`Ellipsis` peut être utilisé pour marquer une méthode comme abstraite, indiquant que la méthode doit être implémentée dans une sous-classe.

   ```python
   class AbstractClass:
       def abstract_method(self):
           ...
   ```

4. **Annotations de type (typing) :**
   Dans certains cas, l'`Ellipsis` peut être utilisé avec des annotations de type pour indiquer que le type est générique ou indéterminé.

   ```python
   from typing import Any

   def func_with_unknown_args(*args: Any, **kwargs: Any) -> None:
       ...
   ```

5. **Marquer des passages de code ignorés dans les blocs `if __name__ == "__main__"` :**

   L'`Ellipsis` peut être utilisé pour marquer des parties de code ignorées dans des blocs conditionnels pour des tests ou des scripts exécutés directement.

   ```python
   if __name__ == "__main__":
       ...
   ```

6. **Désignation d'un indice complet dans des objets personnalisés :**
   Lors de la définition de classes personnalisées qui implémentent la méthode `__getitem__`, l'`Ellipsis` peut être utilisé pour désigner des indices complets ou des comportements spécifiques.

   ```python
   class MyClass:
       def __getitem__(self, key):
           if key == ...:
               return "Ellipsis used"
           else:
               return "Key used"

   obj = MyClass()
   print(obj[...])  # Affiche "Ellipsis used"
   ```

Ces cas montrent la polyvalence de l'`Ellipsis` en Python, même si son utilisation reste relativement rare par rapport à d'autres éléments du langage.

## Séquences immuables

Nous l'avons vu durant le cours certains objets complexes comme les listes ou les dictionnaires ne peuvent pas être hachés, c'est-à-dire qu'ils ne peuvent pas être utilisés comme clés dans un dictionnaire ou stockés dans un ensemble.

Vous ne pouvez pas faire `hash({})` ou `hash([])`.

Ceci nous amène à la notion de séquences immuables. Les séquences immuables sont des objets qui ne peuvent pas être modifiés après leur création. Les types de séquences immuables les plus courants en Python sont:

1. les chaînes de caractères,
2. les tuples et
3. les `frozenset`.

Lorsque vous ajouter un caractère à une chaîne de caractères, Python crée une nouvelle chaîne de caractères. En voici la preuve :

```python
>>> s = "hello"
>>> hex(id(s))
'0x7f8b1c1b3b70'
>>> s += " world"  # On s'attend à juste étendre la chaîne de caractères
>>> hex(id(s))  # Mais on obtient une nouvelle adresse mémoire
'0x7f7849c9e7f0'
```

## Le garbage collector

On peut se demander ce qui se passe de l'ancienne valeur de `s` ? Python est un langage de programmation qui gère la mémoire de manière automatique. Lorsque la variable `s` est réaffectée, Python libère automatiquement la mémoire de l'ancienne valeur de `s` qui n'est plus utilisée.

Le système utilisé par Python pour gérer la mémoire est appelé *garbage collector*. Le garbage collector est un processus qui s'exécute en arrière-plan et qui est responsable de la libération de la mémoire des objets qui ne sont plus utilisés par le programme.

La fonction `sys.getrefcount()` permet de connaître le nombre de références d'un objet. Par exemple, si on exécute `sys.getrefcount(s)` après la réaffectation de `s`, on obtient 2. Cela signifie que `s` est référencé par deux variables. Notons que `sys.getrefcount()` renvoie toujours un nombre supérieur ou égal au nombre réel de références puisqu'il en crée une temporaire pour l'appel de la fonction.

D'après vous quel est le nombre de référence à la variable `v` ?

```python
v = 1
u = [v, v, v, v, v]
```

> [!TIP]
> Pour afficher le nombre de références d'une variable, vous pouvez utiliser la fonction `sys.getrefcount()` et importer le module `sys`.

Lorsque le nombre de référence sur un objet tombe à zéro, le garbage collector libère la mémoire de cet objet, c'est-à-dire qu'il le détruit. En C on utiliserait la fonction `free()` pour libérer la mémoire allouée à un objet.

Ceci nous amène à penser que Python ne fonctionne qu'avec de l'allocation dynamique de mémoire. La plupart des objets sont alloués sur le `heap`, en C ce serait l'équivalent de l'allocation dynamique de mémoire avec `malloc()`.

## Les Callables

Expliqué [ici](https://docs.python.org/3/reference/datamodel.html#callable-types), un objet est dit *callable* s'il peut être appelé comme une fonction. Pour vérifier si un objet est *callable*, on peut utiliser la fonction `callable()`.

```python
def foo():
    pass

assert callable(foo) == True
```

Un objet appelable défini certains attributs spéciaux:

- `__call__()` : une méthode qui sera appelée lorsqu'on appelle l'objet.
- `__defaults__` : les valeurs par défaut des arguments de la fonction.
- `__doc__` : la documentation de la fonction.

```python
def add(a, b=42):
    """ Add two numbers """
    return a + b

assert add.__doc__ == " Add two numbers "
assert add.__defaults__ == (42,)
```

On peut évidemment appeler la fonction `add` de la manière suivante:

```python
add.__call__(1, 2)
```

N'oubliez pas que Python est un langage reflexif, c'est-à-dire qu'il permet de manipuler les objets comme des objets de première classe. C'est pour cela que l'on peut manipuler les fonctions comme des objets. On peut même obtenir le code source d'une fonction en utilisant la fonction `inspect.getsource()`.

Essayez de récupérer le code source de la fonction `add` en utilisant la fonction `inspect.getsource()`.

## Les objets

On sait que les objets en Python sont des instances de classes. On sait également qu'un tas de méthodes spéciales permettent de définir le comportement d'un objet. Par exemple, la méthode `__str__()` permet de définir la représentation d'un objet sous forme de chaîne de caractères.

Il y a deux méthodes qui sont difficiles à comprendre c'est le `__init__()` et le `__new__()`. Le `__new__()` est une méthode statique qui est appelée avant le `__init__()` et qui est responsable de la création de l'objet.

### `__new__`

Dans un langage objet comme C++ ou Java on parle de *constructeur* pour désigner la méthode qui est appelée lors de la création d'un objet. En Python, le `__new__()` est le constructeur de la classe.

Il est rare de devoir utiliser le `__new__()` dans une classe à moins d'avoir recours à des besoins très spécifiques.

Un cas typique est le Singleton. Le Singleton est un patron de conception utilisé en génie logiciel. Il garantit qu'une classe n'a qu'une seule instance et fournit un point d'accès global à cette instance.

Le Singleton est utilisé par exemple pour faire du logging, ou pour gérer des configurations globales d'un projet.

L'exemple le plus courant est:

```python
import logging

logger = logging.getLogger(__name__)
```

Ici on demande au module de logging de nous retourner un objet `Logger` qui est unique pour le module courant, c'est un Singleton.

Voici l'exemple d'un Singleton qui est un compteur unique:

```python
class CounterSingleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super(CounterSingleton, cls).__new__(cls)
            cls._instance.counter = 0
        return cls._instance

    def increment(self):
        self.counter += 1

    def __int__(self):
        return self.counter

    def value(self):
        return self.counter
```

On peut utiliser ce Singleton de la manière suivante:

```python
s1 = CounterSingleton()
s1.increment()
a = s1.value() # a = 1

s2 = CounterSingleton()
s2.increment()
b = s2.value() # b = 2

def fonction():
    s3 = CounterSingleton()
    s3.increment()
    return s3.value()

c = fonction() # c = 3
```

Ici le `__new__` assigne l'instance à un attribut caché de classe `_instance`. Si l'instance n'existe pas, on crée une nouvelle instance de la classe `CounterSingleton` et on l'assigne à l'attribut `_instance`.

Si l'on souhaite hériter d'une classe immuable comme `int`, on doit surcharger le `__new__()` et non le `__init__()`.

```python
class MyInt(int):
    def __new__(cls, value):
        return super(MyInt, cls).__new__(cls, value)

    def __init__(self, value):
        super(MyInt, self).__init__()
        self.value = value

    def add_answer_to_everything(self):
        return self + 42

i = MyInt(42)
assert isinstance(i, int) == True
```

### `__repr__`
Le `__repr__()` est une méthode spéciale qui permet de définir la représentation d'un objet lorsqu'on l'affiche dans la console. Cette méthode est pratique pour représenter facilement des objets spéciaux. Imaginons une matrice, on peut définir une méthode `__repr__()` pour afficher la matrice de manière lisible.

```python
class Matrix:
    def __init__(self, matrix):
        self.matrix = matrix

    def __repr__(self):
        return "\n".join([" ".join([str(x) for x in row]) for row in self.matrix])

>>> u = [[1,2,3], [4,5,6], [7,8,9]]
[[1, 2, 3], [4, 5, 6], [7, 8, 9]]  # Pas très joli

>>> Matrix(u)
1 2 3
4 5 6
7 8 9  ## Plus joli
```

### `__lt__`, `__le__`, `__eq__`, `__ne__`, `__gt__`, `__ge__`

Ces méthodes spéciales permettent de définir le comportement des opérateurs de comparaison `<`, `<=`, `==`, `!=`, `>`, `>=`.

Les opérateurs de comparaison sont parfois nécessaires dans des structures de données complexes comme les arbres binaires.

Pour faire une recherche dichotomique dans une liste d'objet, ou trier cette liste, il est nécessaire de pouvoir les comparer.

Par exemple définissons un objet qui contient la masse d'un objet et la vitesse à laquelle il se déplace. On peut définir que comparer ces objets revient à comparer l'énergie cinétique de ces objets.

```python
class Object:
    def __init__(self, mass, velocity):
        self.mass = mass
        self.velocity = velocity

u = [Object(1, 2), Object(2, 3), Object(3, 4)]
```

Si vous essayez de trier `u` avec `u.sort()` ou `sorted(u)`, vous obtiendrez une erreur `TypeError: '<' not supported between instances of 'Object' and 'Object'`. En effet, Python ne sait pas comment comparer deux objets de type `Object` et à besoin de la méthode `__lt__` pour le faire.

```python
class Object:
    def __init__(self, mass, velocity):
        self.mass = mass
        self.velocity = velocity

    def __lt__(self, other):
        return 0.5 * self.mass * self.velocity ** 2 < 0.5 * other.mass * other.velocity ** 2
```

Maintenant vous pouvez trier `u` avec `u.sort()` ou `sorted(u)`. Mais le résultat sur la console n'est pas joli. Vous pouvez définir la méthode `__repr__` pour afficher les objets de manière lisible.

```python
>>> sorted(u)
[<__main__.Object at 0x7f85a33ac2b0>,
 <__main__.Object at 0x7f85a33ad480>,
 <__main__.Object at 0x7f85a33af4f0>]
```

Essayez d'implémenter `__repr__` pour afficher les objets par exemple sous la forme `Object(m=1 kg, v=2 m/s, E=2 J)`.
```

### `__hash__`

On a souvent parlé qu'un objet doit être immuable pour être haché. En effet, pour être utilisé comme clé dans un dictionnaire, un objet doit être haché. Pour cela, il doit implémenter la méthode `__hash__`.

Par défaut un objet est haché par son adresse mémoire. Si vous avez deux objets identiques, ils auront deux adresses mémoires différentes et donc deux hachages différents et seront considérés comme deux objets différents.

```python
>>> u = {Object(1, 2) : 'a'}
>>> u[Object(1, 2)]
KeyError
```

Pour que deux objets soient considérés comme identiques, il faut que leur hachage soit identique. Pour cela, il faut surcharger la méthode `__hash__` et la méthode `__eq__`.

```python
class Object:
    def __init__(self, mass, velocity):
        self.mass = mass
        self.velocity = velocity

    def __hash__(self):
        return hash((self.mass, self.velocity))

    def __eq__(self, other):
        return self.mass == other.mass and self.velocity == other.velocity
```

Maintenant vous pouvez utiliser `Object` comme clé dans un dictionnaire.

Pour le fun, on va faire bugger le dictionnaire en modifiant la masse de l'objet après l'avoir utilisé comme clé dans le dictionnaire.

```python
>>> u = {Object(1, 2) : 'a'}
>>> list(u.keys())[0].mass = 42
>>> u
KeyError
```

L'explication est que le dictionnaire s'attend à ce que les objets soient immuables et comment nous avons modifié la clé, le dictionnaire ne peut plus fonctionner correctement. Une solution serait d'interdir le changement des attributs en les cachants et en ajoutant un *getter* pour obtenir la masse et la vitesse.

### `__getattr__` et `__getattribute__`

Lorsque vous utilisez le `.` pour l'autocomplétion dans votre terminal REPL, vous demandez à Python d'appeler la méthode `__getattribute__`. On peut s'amuser à voir ce que Python ferait dans ce cas:

```