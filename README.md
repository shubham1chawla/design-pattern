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
## Structural Patterns

These patterns explain how to assemble objects and classes into larger structures while keeping these structures flexible and efficient.

### Adapter (Wrapper)

An adapter is a structural design pattern that allows objects with incompatible interfaces to collaborate.

Let's consider the following example of holes and pegs.

```python
class RoundPeg:
  def __init__(self, radius: float):
    self.radius = radius

class RoundHole:
  def __init__(self, radius: float):
    self.radius = radius

  def fits(peg: RoundPeg) -> bool:
    return self.radius >= peg.radius
```

In this example, `RoundPeg` is compatible with `RoundHole`. But what happens if we have a `SquarePeg` and want to make it compatible with `RoundHole`?

```python
class SquarePeg:
  def __init__(self, width: float):
    self.width = width
```

Since `SquarePeg` has no overlap with `RoundPeg`, we can't use `RoundHole` with `SquarePeg`. We can implement the following to make them compatible.

```python
class SquarePegAdapter(RoundPeg): # <-- Adapters extend the type we wish to mimic
  def __init__(self, peg: SquarePeg):
    self.peg = peg

  @property
  def width(self):
    return self.peg.width * sqrt(2) / 2 # <-- Calculation to find the radius from width
```

Since we extend `RoundPeg`, we can pass it to the `RoundHole`'s `fits` method without issues.

### Bridge

A bridge is a structural design pattern that lets you split a large class or a set of closely related classes into two separate hierarchies: abstraction and implementation.

Let's understand this method using the following example.

```python
from abc import ABC, abstractmethod

class Device(ABC):
  def __init__(self):
    self.on = False

  @abstractmethod
  def toggle_power(self):
    pass

class Remote:
  def __init__(self, device: Device):
    self.device = device

  def turn_on(self):
    # Logic

  def turn_off(self):
    # Logic
```

In this example, the `Device` is an abstract class, and concrete classes implement these functionalities differently. On the other hand, the `Remote` class "has" a device to work with and is not concerned with "how" concrete classes implement said functionalities.

### Composite (Object Tree)

Composite is a structural design pattern that lets you compose objects into tree structures and then work with these structures as if they were individual objects.

Consider the following graphical elements with `move` and `draw` functionalities.

```ts
interface Graphic {
  move: (x: int, y: int) => void;
  draw: () => void;
}

class Dot implements Graphic {
  // Logic to move and draw a dot
}

class Circle extends Dot {
  // Logic to move and draw a circle
}
```

An image can have multiple graphical elements, which we can represent as follows.

```ts
class CompoundGraphic implements Graphic {
  children: Graphic[]

  move(x: int, y: int): void {
    for (const graphic of this.children) {
      graphic.move(x, y); // <-- Note that we delegate tasks to children nodes
    }
  }

  // Logic to add graphics
}
```

In a composite pattern, we delegate tasks to the leaf nodes, which perform the business logic independently.

### Decorator (Wrapper)

A decorator is a structural design pattern that lets you attach new behaviors to objects by placing these objects inside particular wrapper objects that contain the behaviors.

> [!NOTE]
> You can consider `SquarePegAdapter` to be a "decorator" in the Adaptor example.

### Facade

A facade is a structural design pattern that provides a simplified interface to a library, a framework, or any other complex set of classes.

> [!NOTE]
> Essentially, any complex library or service you use, for instance, node packages or Rest APIs, hides away complex functionalities and exposes APIs you consume.

### Flyweight (Cache)

Flyweight is a structural design pattern that lets you fit more objects into the available amount of RAM by sharing common parts of the state between multiple objects instead of keeping all the data in each object.

> [!NOTE]
> Read more about it [here](https://refactoring.guru/design-patterns/flyweight).

### Proxy

Proxy is a structural design pattern that lets you provide a substitute or placeholder for another object. A proxy controls access to the original object, allowing you to perform something before or after the request gets through to the original object.

> [!TIP]
> Spring AOP uses the proxy pattern to perform actions before, after, or around the target method.

Let's consider the following example of a YouTube API library.

```ts
interface IYouTubeAPIService {
  list: () => string[];
}

class YouTubeAPIService implements IYouTubeAPIService {
  list(): string[] {
    // Returns the list of videos
  }
}

class YouTubeAPICacheProxy implements IYouTubeAPIService {
  cachedVideos: string[]

  constructor(service: YouTubeAPIService) {
    this.service = service
  }

  list(): string[] {
    if (!this.cachedVideos) {
      this.cachedVideos = this.service.list();
    }
    return this.cachedVideos;
  }
}
```

Notice that the proxy class intercepts operations and performs its logic.
