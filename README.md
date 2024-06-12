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

Dans un langage objet comme C++ ou Java on parle de *constructeur* pour désigner la méthode qui est appelée lors de la création d'un objet. En Python, le `__new__()` est le constructeur de la classe.

Il est rare de devoir utiliser le `__new__()` dans une classe à moins de vouloir créer un objet immuable, par exemple, le `tuple`.

```python
class MyImmutable
    def __new__(cls, *args, **kwargs):
        return super(MyImmutable, cls).__new__(cls)

    def __init__(self, a, b):
        self.a = a
        self.b = b

    def __setattr__(self, name, value):
        raise AttributeError("Can't set attribute")

    def __delattr__(self, name):
        raise AttributeError("Can't delete attribute")
```