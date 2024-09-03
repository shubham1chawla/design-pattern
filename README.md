# Design Patterns

> [!NOTE]
> I have referred [Refactoring Guru](https://refactoring.guru/design-patterns/catalog) and YouTube videos ([Fireship](https://www.youtube.com/watch?v=tv-_1er1mWI) and [NeetCode](https://www.youtube.com/watch?v=tAuRQs_d9F8)) to write this README.

We can categorize design patterns as -
1. Creational
2. Structural
3. Behavioral

## Creational Patterns

These patterns provide various object creation mechanisms, which increase flexibility and reuse of existing code.

### Factory

Imagine you want to produce burgers but want to get only some of the ingredients. Then, you can use this pattern. 
Let's say you have the following `Burger` class you wish to create.

```python
class Burger:
  def __init__(self, ingredients: List[str]):
    self.ingredients = ingredients
```

You can create different burgers like this - 

```python
vegan_burger = Burger(['bun', 'sauce', 'veggie-patty'])
cheese_burger = Burger(['bun', 'cheese', 'beef-patty'])
```

Creating these instances requires knowing the ingredients. You can make a factory that can abstract the ingredients for you and give you the burgers without caring about the ingredients.

```python
def BurgerFactory:
  def create_vegan_bruger(self) -> Burger:
    return Burger(['bun', 'sauce', 'veggie-patty'])

  def create_cheese_burger(self) -> Burger:
    return Burger(['bun', 'cheese', 'beef-patty'])
```

A practical use case for this method comes when building a cross-platform app. Please take a look at the following example.

```python
class IOSButton:
  def __init__(self):
    # Code to create a button for IOS
    pass

class AndroidButton:
  def __init__(self):
    # Code to create a button for Android
    pass

class ButtonFactory:
  def create_button(self, os: str):
    return IOSButton() if os == 'ios' else AndroidButton()
```

### Abstract Factory

Extending the previous example of a cross-platform `ButtonFactory`, a button is just one UI element, but you'll need other elements, too. 

Let's consider the following abstract factory that produces OS-agnostic UI elements.

```python
from abc import ABC, abstractmethod

class AbstractUIFactory(ABC):

  @abstractmethod
  def create_button(self) -> 'Button':
    pass

  @abstractmethod
  def create_checkbox(self) -> 'Checkbox':
    pass
```

An IOS implementation of this abstract factory can look like the following.

```python
class IOSUIFactory(AbstractUIFactory):

  def create_button(self) -> 'Button':
    return IOSButton()

  def create_checkbox(self) -> 'Checkbox':
    return IOSCheckbox()
```

An Android implementation of this abstract factory can look like the following.

```python
class AndroidUIFactory(AbstractUIFactory):

  def create_button(self) -> 'Button':
    return AndroidButton()

  def create_checkbox(self) -> 'Checkbox':
    return AndroidCheckbox()
```

### Builder

One of the drawbacks of the `BurgerFactory` example previously discussed was the inability to modify ingredients. To address this drawback, let's consider the following builder pattern.

> [!NOTE]
> We'll modify the `Burger` class for this example.

```python
class Burger:
  def __init__(self):
    self.buns = None
    self.patty = None
    self.sauce = None
    self.cheese = None

  # Setters
```

```python
class BurgerBuilder:
  def __init__(self):
    self.burger = Burger()

  def buns(self, buns: str) -> 'BurgerBuilder':
    self.burger.set_buns(buns)
    return self # <-- Returning self here ensures that we can chain these methods

  def patty(self, patty: str) -> 'BurgerBuilder':
    self.burger.set_patty(patty)
    return self

  def sauce(self, sauce: str) -> 'BurgerBuilder':
    self.burger.set_sauce(sauce)
    return self

  def cheese(self, cheese: str) -> 'BurgerBuilder':
    self.burger.set_cheese(cheese)
    return self

  # Build method returns the final object
  def build(self) -> 'Burger':
    return self.burger
```

We can initiate the builder class and choose the appropriate ingredients.

```python
burger = BurgerBuilder().buns('sesame').patty('fish-patty').sauce('secret-sauce').build()
```

### Prototype (Clone)

Prototype is a creational design pattern that lets you copy existing objects without making your code dependent on their classes.

Consider the following abstract class, `Shape`, to understand this method.

```python
from abc import ABC, abstractmethod
from typing import Optional

class Shape(ABC):
  def __init__(self, source: Optional[Shape]):
    self.x = source.x if source else None
    self.y = source.y if source else None
    self.color = source.color if source else None

  @abstractmethod
  def clone() -> 'Shape':
    pass
```

By defining a `clone` method in the abstract class, we ensure that all concrete implementations of the `Shape` class can clone themselves. Having the constructor accept a source object ensures we can copy values from the source instance.

The concrete implementation of the `Shape` class looks like the following examples.

```python
class Rectangle(Shape):
  def __init__(self, source: Optional[Rectangle]):
    super(source)
    self.width = source.width if source else None
    self.height = source.height if source else None

  def clone(self) -> 'Shape':
    return Rectangle(this)

class Circle(Shape):
  def __init__(self, source: Optional[Circle]):
    super(source)
    self.radius = source.radius if source else None

  def clone(self) -> 'Shape':
    return Circle(this)
```

### Singleton

Singleton is a creational design pattern that lets you ensure that a class has only one instance while providing a global access point to this instance.

Using the `Database` class, let's consider a practical example of the Singleton pattern.

```ts
class Database {
  private static instance?: Database

  // Having a private constructor ensures no one can create an instance of a database from outside.
  private constructor() {}

  getInstance(): Database {
    if (!Database.instance) {
      Database.instance = new Database();
    }
    return Database.instance;
  }

  query(sql: string): Promise<any> {
    // Performing SQL queries...
  }
}
```
