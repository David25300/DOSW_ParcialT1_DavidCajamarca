# 🧩 Patrones de Diseño en Programación

Los **patrones de diseño** son soluciones reutilizables a problemas comunes que surgen durante el desarrollo de software. Fueron popularizados por el libro *"Design Patterns: Elements of Reusable Object-Oriented Software"* (1994) del conocido **Gang of Four (GoF)**.

Se clasifican en tres grandes categorías:

| Categoría | Propósito | Cantidad |
|-----------|-----------|----------|
| **Creacionales** | Cómo se crean los objetos | 5 |
| **Estructurales** | Cómo se componen clases y objetos | 7 |
| **De Comportamiento** | Cómo interactúan y se comunican los objetos | 11 |

---

## 📐 Estructura General de un Patrón

Cada patrón de diseño se describe típicamente con los siguientes elementos:

- **Nombre**: Identifica el patrón.
- **Problema**: Qué problema resuelve.
- **Solución**: Descripción abstracta de cómo lo resuelve.
- **Participantes**: Clases/objetos involucrados y sus roles.
- **Diagrama**: Representación visual (UML).
- **Consecuencias**: Ventajas y desventajas de aplicarlo.

---

## 🏗️ 1. Patrones Creacionales

Se enfocan en **abstraer el proceso de instanciación** de objetos, haciendo que el sistema sea independiente de cómo se crean, componen y representan sus objetos.

---

### 1.1 Singleton

**Propósito:** Garantizar que una clase tenga **una única instancia** y proporcionar un punto de acceso global a ella.

**Problema que resuelve:** Cuando necesitas exactamente un objeto para coordinar acciones en todo el sistema (ej: conexión a base de datos, logger, configuración).

**Estructura:**

```
┌─────────────────────────┐
│       Singleton          │
├─────────────────────────┤
│ - instance: Singleton    │
├─────────────────────────┤
│ - Singleton()            │
│ + getInstance(): Singleton│
└─────────────────────────┘
```

**Participantes:**
- `Singleton`: Clase que define el método `getInstance()` que retorna la única instancia.

**Ejemplo en Java:**

```java
public class DatabaseConnection {

    private static volatile DatabaseConnection instance;

    private DatabaseConnection() {
        // Constructor privado para evitar instanciación externa
    }

    public static DatabaseConnection getInstance() {
        if (instance == null) {
            synchronized (DatabaseConnection.class) {
                if (instance == null) {
                    instance = new DatabaseConnection();
                }
            }
        }
        return instance;
    }

    public void query(String sql) {
        System.out.println("Ejecutando: " + sql);
    }
}

// Uso
// DatabaseConnection db = DatabaseConnection.getInstance();
// db.query("SELECT * FROM users");
```

**Cuándo usarlo:** Cuando debe existir exactamente una instancia de una clase accesible desde un punto conocido.

---

### 1.2 Factory Method

**Propósito:** Definir una interfaz para crear objetos, pero **delegar a las subclases** la decisión de qué clase concreta instanciar.

**Problema que resuelve:** Cuando una clase no puede anticipar el tipo de objetos que debe crear.

**Estructura:**

```
┌───────────────┐          ┌───────────────┐
│   Creator     │          │   Product     │
│ (abstracto)   │          │ (interfaz)    │
├───────────────┤          └───────┬───────┘
│ + factoryMethod()│               │
│ + operation() │          ┌───────┴───────┐
└───────┬───────┘          │ConcreteProduct│
        │                  └───────────────┘
┌───────┴───────┐
│ConcreteCreator│
├───────────────┤
│ + factoryMethod()│
└───────────────┘
```

**Participantes:**
- `Product`: Interfaz de los objetos que crea el factory method.
- `ConcreteProduct`: Implementación concreta del producto.
- `Creator`: Declara el factory method.
- `ConcreteCreator`: Sobrescribe el factory method para crear un `ConcreteProduct`.

**Ejemplo en Java:**

```java
// Producto
interface Transport {
    String deliver();
}

class Truck implements Transport {
    @Override
    public String deliver() {
        return "Entrega por tierra en camión";
    }
}

class Ship implements Transport {
    @Override
    public String deliver() {
        return "Entrega por mar en barco";
    }
}

// Creator
abstract class Logistics {
    public abstract Transport createTransport();

    public String planDelivery() {
        Transport transport = createTransport();
        return transport.deliver();
    }
}

class RoadLogistics extends Logistics {
    @Override
    public Transport createTransport() {
        return new Truck();
    }
}

class SeaLogistics extends Logistics {
    @Override
    public Transport createTransport() {
        return new Ship();
    }
}

// Uso
// Logistics logistics = new SeaLogistics();
// System.out.println(logistics.planDelivery());
```

**Cuándo usarlo:** Cuando una clase no sabe de antemano qué tipo de objetos necesita crear.

---

### 1.3 Abstract Factory

**Propósito:** Proporcionar una interfaz para crear **familias de objetos relacionados** sin especificar sus clases concretas.

**Problema que resuelve:** Cuando el sistema debe ser independiente de cómo se crean y componen sus productos, y trabaja con múltiples familias de productos.

**Estructura:**

```
┌──────────────────┐       ┌──────────────┐  ┌──────────────┐
│ AbstractFactory  │       │AbstractProductA│ │AbstractProductB│
├──────────────────┤       └──────┬───────┘  └──────┬───────┘
│+createProductA() │              │                  │
│+createProductB() │       ┌──────┴───────┐  ┌──────┴───────┐
└────────┬─────────┘       │ProductA1     │  │ProductB1     │
         │                 │ProductA2     │  │ProductB2     │
┌────────┴─────────┐       └──────────────┘  └──────────────┘
│ConcreteFactory1  │
│ConcreteFactory2  │
└──────────────────┘
```

**Participantes:**
- `AbstractFactory`: Interfaz para crear cada tipo de producto.
- `ConcreteFactory`: Implementa las operaciones de creación para una familia específica.
- `AbstractProduct`: Interfaz para un tipo de producto.
- `ConcreteProduct`: Implementación específica de un producto.

**Ejemplo en Java:**

```java
// Productos abstractos
interface Button {
    void render();
}

interface Checkbox {
    void render();
}

// Productos concretos - Windows
class WindowsButton implements Button {
    @Override
    public void render() {
        System.out.println("Renderizando botón estilo Windows");
    }
}

class WindowsCheckbox implements Checkbox {
    @Override
    public void render() {
        System.out.println("Renderizando checkbox estilo Windows");
    }
}

// Productos concretos - Mac
class MacButton implements Button {
    @Override
    public void render() {
        System.out.println("Renderizando botón estilo Mac");
    }
}

class MacCheckbox implements Checkbox {
    @Override
    public void render() {
        System.out.println("Renderizando checkbox estilo Mac");
    }
}

// Abstract Factory
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

class WindowsFactory implements GUIFactory {
    @Override
    public Button createButton() { return new WindowsButton(); }
    @Override
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}

class MacFactory implements GUIFactory {
    @Override
    public Button createButton() { return new MacButton(); }
    @Override
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}

// Uso
// GUIFactory factory = new MacFactory();
// Button btn = factory.createButton();
// btn.render();
```

**Cuándo usarlo:** Cuando el sistema debe trabajar con múltiples familias de productos relacionados.

---

### 1.4 Builder

**Propósito:** Separar la **construcción de un objeto complejo** de su representación, para que el mismo proceso de construcción pueda crear distintas representaciones.

**Problema que resuelve:** Cuando la creación de un objeto implica muchos pasos o configuraciones opcionales (evita constructores con decenas de parámetros).

**Estructura:**

```
┌───────────┐      ┌──────────────────┐
│  Director  │─────▶│   Builder        │
├───────────┤      │   (interfaz)     │
│+construct()│      ├──────────────────┤
└───────────┘      │+buildPartA()     │
                   │+buildPartB()     │
                   │+getResult()      │
                   └────────┬─────────┘
                            │
                   ┌────────┴─────────┐
                   │ ConcreteBuilder   │
                   ├──────────────────┤
                   │+buildPartA()     │
                   │+buildPartB()     │
                   │+getResult(): Product│
                   └──────────────────┘
```

**Participantes:**
- `Builder`: Interfaz abstracta para crear las partes del producto.
- `ConcreteBuilder`: Construye y ensambla las partes del producto.
- `Director`: Construye el objeto usando la interfaz del Builder.
- `Product`: El objeto complejo resultante.

**Ejemplo en Java:**

```java
class House {
    private String walls;
    private String roof;
    private boolean garage;

    public void setWalls(String walls) { this.walls = walls; }
    public void setRoof(String roof) { this.roof = roof; }
    public void setGarage(boolean garage) { this.garage = garage; }

    @Override
    public String toString() {
        return "Casa [paredes=" + walls + ", techo=" + roof +
               ", garaje=" + garage + "]";
    }
}

class HouseBuilder {
    private House house = new House();

    public HouseBuilder buildWalls(String material) {
        house.setWalls(material);
        return this; // Permite encadenamiento (fluent interface)
    }

    public HouseBuilder buildRoof(String roofType) {
        house.setRoof(roofType);
        return this;
    }

    public HouseBuilder buildGarage(boolean hasGarage) {
        house.setGarage(hasGarage);
        return this;
    }

    public House build() {
        House result = house;
        house = new House(); // Reset para reutilizar el builder
        return result;
    }
}

// Uso con encadenamiento
// House house = new HouseBuilder()
//     .buildWalls("ladrillo")
//     .buildRoof("teja")
//     .buildGarage(true)
//     .build();
```

**Cuándo usarlo:** Cuando el algoritmo de creación debe ser independiente de las partes que conforman el objeto y de cómo se ensamblan.

---

### 1.5 Prototype

**Propósito:** Crear nuevos objetos **clonando una instancia existente** (prototipo), en lugar de crearlos desde cero.

**Problema que resuelve:** Cuando la creación de objetos es costosa y es más eficiente copiar un objeto existente.

**Estructura:**

```
┌──────────────────┐
│    Prototype     │
│   (interfaz)     │
├──────────────────┤
│ + clone(): Prototype│
└────────┬─────────┘
         │
┌────────┴─────────┐
│ConcretePrototype  │
├──────────────────┤
│ + clone(): Prototype│
└──────────────────┘
```

**Participantes:**
- `Prototype`: Interfaz que declara el método `clone()`.
- `ConcretePrototype`: Implementa la operación de clonarse a sí mismo.

**Ejemplo en Java:**

```java
abstract class Shape implements Cloneable {
    private String color;
    private int x;
    private int y;

    public Shape(String color, int x, int y) {
        this.color = color;
        this.x = x;
        this.y = y;
    }

    // Constructor de copia (para clone)
    protected Shape(Shape source) {
        this.color = source.color;
        this.x = source.x;
        this.y = source.y;
    }

    @Override
    public abstract Shape clone();

    // Getters y setters
    public String getColor() { return color; }
    public void setColor(String color) { this.color = color; }
    public int getX() { return x; }
    public int getY() { return y; }
}

class Circle extends Shape {
    private int radius;

    public Circle(String color, int x, int y, int radius) {
        super(color, x, y);
        this.radius = radius;
    }

    private Circle(Circle source) {
        super(source);
        this.radius = source.radius;
    }

    @Override
    public Circle clone() {
        return new Circle(this);
    }
}

// Uso
// Circle original = new Circle("rojo", 10, 20, 5);
// Circle copia = original.clone();
// copia.setColor("azul"); // No afecta al original
```

**Cuándo usarlo:** Cuando las instancias de una clase solo pueden tener unas pocas combinaciones de estado y es más conveniente clonar prototipos que instanciar manualmente.

---

## 🔗 2. Patrones Estructurales

Se ocupan de **cómo se componen las clases y los objetos** para formar estructuras más grandes y flexibles, manteniendo la eficiencia.

---

### 2.1 Adapter

**Propósito:** Convertir la **interfaz de una clase en otra interfaz** que el cliente espera. Permite que clases incompatibles trabajen juntas.

**Problema que resuelve:** Cuando quieres usar una clase existente pero su interfaz no es compatible con el resto de tu código.

**Estructura:**

```
┌────────┐      ┌───────────────┐      ┌────────────┐
│ Client │─────▶│   Target      │      │  Adaptee   │
└────────┘      │  (interfaz)   │      ├────────────┤
                ├───────────────┤      │+specificOp()│
                │+request()     │      └──────┬─────┘
                └───────┬───────┘             │
                        │                     │
                ┌───────┴───────┐             │
                │   Adapter     │─────────────┘
                ├───────────────┤
                │+request()     │  → llama a adaptee.specificOp()
                └───────────────┘
```

**Participantes:**
- `Target`: Interfaz que el cliente utiliza.
- `Adaptee`: Clase existente con interfaz incompatible.
- `Adapter`: Adapta la interfaz de `Adaptee` a `Target`.

**Ejemplo en Java:**

```java
// Clase existente con interfaz incompatible
class EuropeanSocket {
    public String provideElectricity() {
        return "220V desde enchufe europeo";
    }
}

// Interfaz que el cliente espera
interface USASocket {
    String providePower();
}

// Adapter
class USASocketAdapter implements USASocket {
    private final EuropeanSocket europeanSocket;

    public USASocketAdapter(EuropeanSocket europeanSocket) {
        this.europeanSocket = europeanSocket;
    }

    @Override
    public String providePower() {
        String energy = europeanSocket.provideElectricity();
        return "110V convertido desde (" + energy + ")";
    }
}

// Uso
// USASocket socket = new USASocketAdapter(new EuropeanSocket());
// System.out.println(socket.providePower());
```

**Cuándo usarlo:** Cuando necesitas integrar una clase cuya interfaz no coincide con la que necesitas.

---

### 2.2 Bridge

**Propósito:** Desacoplar una **abstracción de su implementación** para que ambas puedan variar independientemente.

**Problema que resuelve:** Cuando una abstracción puede tener múltiples implementaciones y quieres evitar una explosión de subclases.

**Estructura:**

```
┌──────────────┐             ┌──────────────────┐
│ Abstraction  │────────────▶│ Implementation   │
├──────────────┤  (tiene)    │   (interfaz)     │
│+operation()  │             ├──────────────────┤
└──────┬───────┘             │+operationImpl()  │
       │                     └────────┬─────────┘
┌──────┴───────┐                      │
│RefinedAbstr. │             ┌────────┴─────────┐
└──────────────┘             │ConcreteImplA     │
                             │ConcreteImplB     │
                             └──────────────────┘
```

**Participantes:**
- `Abstraction`: Define la interfaz de abstracción y mantiene una referencia al implementador.
- `RefinedAbstraction`: Extiende la abstracción.
- `Implementor`: Interfaz para las clases de implementación.
- `ConcreteImplementor`: Implementación concreta.

**Ejemplo en Java:**

```java
// Implementación
interface Renderer {
    String renderCircle(double radius);
}

class SVGRenderer implements Renderer {
    @Override
    public String renderCircle(double radius) {
        return "<circle r='" + radius + "'/>";
    }
}

class CanvasRenderer implements Renderer {
    @Override
    public String renderCircle(double radius) {
        return "canvas.drawCircle(" + radius + ")";
    }
}

// Abstracción
abstract class Shape {
    protected Renderer renderer;

    public Shape(Renderer renderer) {
        this.renderer = renderer;
    }

    public abstract String draw();
}

class Circle extends Shape {
    private double radius;

    public Circle(Renderer renderer, double radius) {
        super(renderer);
        this.radius = radius;
    }

    @Override
    public String draw() {
        return renderer.renderCircle(radius);
    }
}

// Uso: misma abstracción, diferente implementación
// Shape circle1 = new Circle(new SVGRenderer(), 5);
// Shape circle2 = new Circle(new CanvasRenderer(), 5);
```

**Cuándo usarlo:** Cuando quieres evitar una vinculación permanente entre abstracción e implementación.

---

### 2.3 Composite

**Propósito:** Componer objetos en **estructuras de árbol** para representar jerarquías parte-todo. Permite tratar objetos individuales y composiciones de forma uniforme.

**Problema que resuelve:** Cuando necesitas representar jerarquías de objetos y quieres que los clientes traten a los objetos simples y compuestos de la misma manera.

**Estructura:**

```
              ┌──────────────┐
              │  Component   │
              │  (interfaz)  │
              ├──────────────┤
              │+operation()  │
              └──────┬───────┘
                     │
          ┌──────────┴──────────┐
   ┌──────┴──────┐     ┌───────┴───────┐
   │    Leaf     │     │   Composite   │
   ├─────────────┤     ├───────────────┤
   │+operation() │     │-children[]    │
   └─────────────┘     │+add(Component)│
                       │+remove()      │
                       │+operation()   │ → itera hijos
                       └───────────────┘
```

**Participantes:**
- `Component`: Interfaz común para todos los objetos de la composición.
- `Leaf`: Objeto hoja sin hijos.
- `Composite`: Almacena hijos y delega operaciones a ellos.

**Ejemplo en Java:**

```java
// Componente
interface FileSystemComponent {
    int getSize();
    String getName();
}

// Hoja
class File implements FileSystemComponent {
    private final String name;
    private final int size;

    public File(String name, int size) {
        this.name = name;
        this.size = size;
    }

    @Override
    public int getSize() { return size; }

    @Override
    public String getName() { return name; }
}

// Composite
class Folder implements FileSystemComponent {
    private final String name;
    private final List<FileSystemComponent> children = new ArrayList<>();

    public Folder(String name) { this.name = name; }

    public void add(FileSystemComponent component) {
        children.add(component);
    }

    public void remove(FileSystemComponent component) {
        children.remove(component);
    }

    @Override
    public int getSize() {
        return children.stream()
                .mapToInt(FileSystemComponent::getSize)
                .sum();
    }

    @Override
    public String getName() { return name; }
}

// Uso
// Folder root = new Folder("src");
// root.add(new File("Main.java", 500));
// Folder models = new Folder("models");
// models.add(new File("User.java", 200));
// root.add(models);
// System.out.println(root.getSize()); // 700
```

**Cuándo usarlo:** Cuando necesitas representar jerarquías de objetos y tratar uniformemente objetos individuales y compuestos.

---

### 2.4 Decorator

**Propósito:** Agregar **responsabilidades adicionales** a un objeto dinámicamente, proporcionando una alternativa flexible a la herencia.

**Problema que resuelve:** Cuando necesitas añadir funcionalidades a objetos individuales sin afectar a otros objetos de la misma clase.

**Estructura:**

```
┌──────────────┐
│  Component   │
│  (interfaz)  │
├──────────────┤
│+operation()  │
└──────┬───────┘
       │
  ┌────┴──────────────────────┐
  │                           │
┌─┴────────────┐    ┌────────┴────────┐
│ConcreteComp. │    │   Decorator     │
├──────────────┤    │  (abstracto)    │
│+operation()  │    ├─────────────────┤
└──────────────┘    │-component       │
                    │+operation()     │ → component.operation()
                    └────────┬────────┘
                             │
                    ┌────────┴────────┐
                    │ConcreteDecorator│
                    ├─────────────────┤
                    │+operation()     │ → añade comportamiento
                    └─────────────────┘
```

**Participantes:**
- `Component`: Interfaz para los objetos a los que se les puede añadir responsabilidades.
- `ConcreteComponent`: Objeto al que se le añaden responsabilidades.
- `Decorator`: Mantiene una referencia al componente y define la interfaz.
- `ConcreteDecorator`: Añade responsabilidades al componente.

**Ejemplo en Java:**

```java
// Componente
interface Coffee {
    double cost();
    String description();
}

// Componente concreto
class SimpleCoffee implements Coffee {
    @Override
    public double cost() { return 1.00; }

    @Override
    public String description() { return "Café simple"; }
}

// Decorator base
abstract class CoffeeDecorator implements Coffee {
    protected final Coffee decoratedCoffee;

    public CoffeeDecorator(Coffee coffee) {
        this.decoratedCoffee = coffee;
    }
}

// Decorators concretos
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) { super(coffee); }

    @Override
    public double cost() { return decoratedCoffee.cost() + 0.50; }

    @Override
    public String description() {
        return decoratedCoffee.description() + " + leche";
    }
}

class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) { super(coffee); }

    @Override
    public double cost() { return decoratedCoffee.cost() + 0.25; }

    @Override
    public String description() {
        return decoratedCoffee.description() + " + azúcar";
    }
}

// Uso: decoradores encadenados
// Coffee coffee = new SugarDecorator(new MilkDecorator(new SimpleCoffee()));
// coffee.description() → "Café simple + leche + azúcar"
// coffee.cost()        → 1.75
```

**Cuándo usarlo:** Cuando la extensión mediante herencia no es práctica y necesitas añadir funcionalidades dinámicamente.

---

### 2.5 Facade

**Propósito:** Proporcionar una **interfaz unificada y simplificada** a un conjunto de interfaces de un subsistema.

**Problema que resuelve:** Cuando un subsistema es complejo y quieres ofrecer una interfaz sencilla para los casos de uso más comunes.

**Estructura:**

```
┌────────┐      ┌──────────────────┐
│ Client │─────▶│     Facade       │
└────────┘      ├──────────────────┤
                │+simpleOperation()│
                └──┬───┬───┬───────┘
                   │   │   │
          ┌────────┘   │   └────────┐
          ▼            ▼            ▼
   ┌────────────┐ ┌──────────┐ ┌──────────┐
   │SubsystemA  │ │SubsystemB│ │SubsystemC│
   └────────────┘ └──────────┘ └──────────┘
```

**Participantes:**
- `Facade`: Conoce qué clases del subsistema son responsables de cada petición y delega.
- `Subsystem classes`: Implementan la funcionalidad del subsistema.

**Ejemplo en Java:**

```java
// Subsistemas complejos
class VideoFile {
    private final String name;
    public VideoFile(String name) { this.name = name; }
    public String getName() { return name; }
}

class CodecFactory {
    public static String extract(VideoFile file) {
        return "codec-" + file.getName();
    }
}

class MPEG4Compressor {
    public byte[] compress(String codec) {
        System.out.println("Comprimiendo a MP4: " + codec);
        return new byte[0];
    }
}

class OGGCompressor {
    public byte[] compress(String codec) {
        System.out.println("Comprimiendo a OGG: " + codec);
        return new byte[0];
    }
}

// Facade
class VideoConverter {
    public byte[] convert(String filename, String format) {
        VideoFile file = new VideoFile(filename);
        String codec = CodecFactory.extract(file);

        if ("mp4".equals(format)) {
            return new MPEG4Compressor().compress(codec);
        } else {
            return new OGGCompressor().compress(codec);
        }
    }
}

// El cliente solo interactúa con la fachada
// VideoConverter converter = new VideoConverter();
// byte[] mp4 = converter.convert("video.ogg", "mp4");
```

**Cuándo usarlo:** Cuando necesitas una interfaz simplificada para un subsistema complejo.

---

### 2.6 Flyweight

**Propósito:** Usar compartición para soportar **grandes cantidades de objetos de grano fino** de manera eficiente.

**Problema que resuelve:** Cuando la aplicación necesita crear un número enorme de objetos similares que consumen mucha memoria.

**Estructura:**

```
┌──────────────────┐        ┌──────────────────┐
│ FlyweightFactory │───────▶│    Flyweight     │
├──────────────────┤        │   (interfaz)     │
│+getFlyweight(key)│        ├──────────────────┤
│-flyweights: Map  │        │+operation(       │
└──────────────────┘        │   extrinsicState)│
                            └────────┬─────────┘
                                     │
                            ┌────────┴─────────┐
                            │ConcreteFlyweight │
                            ├──────────────────┤
                            │-intrinsicState   │ (compartido)
                            └──────────────────┘
```

**Participantes:**
- `Flyweight`: Interfaz a través de la cual los flyweights reciben y actúan sobre el estado extrínseco.
- `ConcreteFlyweight`: Almacena el estado intrínseco (compartible).
- `FlyweightFactory`: Crea y gestiona flyweights, asegurando que se compartan correctamente.

**Ejemplo en Java:**

```java
// Flyweight: estado intrínseco compartido
class TreeType {
    private final String name;
    private final String color;
    private final String texture;

    public TreeType(String name, String color, String texture) {
        this.name = name;
        this.color = color;
        this.texture = texture;
    }

    public void draw(int x, int y) {
        System.out.println("Dibujando " + name + " [" + color + "] en (" + x + "," + y + ")");
    }
}

// Factory que garantiza la compartición
class TreeFactory {
    private static final Map<String, TreeType> treeTypes = new HashMap<>();

    public static TreeType getTreeType(String name, String color, String texture) {
        String key = name + "-" + color + "-" + texture;
        return treeTypes.computeIfAbsent(key, k -> new TreeType(name, color, texture));
    }
}

// Estado extrínseco (posición) + referencia al flyweight
class Tree {
    private final int x;
    private final int y;
    private final TreeType type; // Compartido entre muchos árboles

    public Tree(int x, int y, TreeType type) {
        this.x = x;
        this.y = y;
        this.type = type;
    }

    public void draw() {
        type.draw(x, y);
    }
}

// Uso: miles de árboles, pocos TreeType en memoria
// TreeType pino = TreeFactory.getTreeType("Pino", "verde", "rugosa");
// Tree t1 = new Tree(10, 20, pino);
// Tree t2 = new Tree(30, 40, pino); // Mismo TreeType reutilizado
```

**Cuándo usarlo:** Cuando una aplicación usa una gran cantidad de objetos con estado parcialmente compartible.

---

### 2.7 Proxy

**Propósito:** Proporcionar un **sustituto o representante** de otro objeto para controlar el acceso a este.

**Problema que resuelve:** Cuando necesitas controlar el acceso a un objeto (lazy loading, control de acceso, logging, caché).

**Estructura:**

```
┌────────┐     ┌──────────────┐
│ Client │────▶│   Subject    │
└────────┘     │  (interfaz)  │
               ├──────────────┤
               │+request()    │
               └──────┬───────┘
                      │
           ┌──────────┴──────────┐
    ┌──────┴──────┐     ┌────────┴────────┐
    │ RealSubject │     │     Proxy       │
    ├─────────────┤     ├─────────────────┤
    │+request()   │◀────│-realSubject     │
    └─────────────┘     │+request()       │
                        └─────────────────┘
```

**Participantes:**
- `Subject`: Interfaz común para RealSubject y Proxy.
- `RealSubject`: El objeto real que el proxy representa.
- `Proxy`: Controla el acceso al RealSubject.

**Ejemplo en Java:**

```java
// Subject
interface Database {
    String query(String sql);
}

// Real Subject (pesado de crear)
class RealDatabase implements Database {
    public RealDatabase() {
        System.out.println("Inicializando conexión pesada a BD...");
    }

    @Override
    public String query(String sql) {
        return "Resultado de: " + sql;
    }
}

// Proxy con lazy loading y control de acceso
class DatabaseProxy implements Database {
    private RealDatabase realDatabase; // Lazy: no se crea hasta que se necesita
    private final String userRole;

    public DatabaseProxy(String userRole) {
        this.userRole = userRole;
    }

    @Override
    public String query(String sql) {
        if (!"admin".equals(userRole) && sql.contains("DROP")) {
            throw new SecurityException("Acceso denegado: permisos insuficientes");
        }
        if (realDatabase == null) {
            realDatabase = new RealDatabase(); // Se crea solo al primer uso
        }
        System.out.println("[LOG] Query ejecutada: " + sql);
        return realDatabase.query(sql);
    }
}

// Uso
// Database db = new DatabaseProxy("user");
// db.query("SELECT * FROM users");  // OK
// db.query("DROP TABLE users");     // SecurityException
```

**Cuándo usarlo:** Cuando necesitas un intermediario para controlar acceso, cachear resultados, o diferir la creación de objetos costosos.

---

## 🔄 3. Patrones de Comportamiento

Se enfocan en los **algoritmos y la asignación de responsabilidades** entre objetos, describiendo patrones de comunicación entre ellos.

---

### 3.1 Observer

**Propósito:** Definir una **dependencia uno-a-muchos** entre objetos, de forma que cuando uno cambia de estado, todos sus dependientes son notificados automáticamente.

**Problema que resuelve:** Cuando un cambio en un objeto requiere cambiar otros, y no sabes cuántos objetos necesitan cambiar.

**Estructura:**

```
┌──────────────────┐        ┌──────────────┐
│    Subject       │───────▶│   Observer   │
├──────────────────┤  *     │  (interfaz)  │
│-observers: List  │        ├──────────────┤
│+attach(Observer) │        │+update()     │
│+detach(Observer) │        └──────┬───────┘
│+notify()         │               │
└──────────────────┘        ┌──────┴───────┐
                            │ConcreteObserver│
                            ├──────────────┤
                            │+update()     │
                            └──────────────┘
```

**Participantes:**
- `Subject`: Conoce a sus observadores y provee métodos para suscribirse/desuscribirse.
- `Observer`: Interfaz de actualización para los objetos que necesitan ser notificados.
- `ConcreteObserver`: Mantiene una referencia al sujeto y actualiza su estado.

**Ejemplo en Java:**

```java
// Observer
interface EventListener {
    void update(String eventType, String data);
}

// Subject (publicador de eventos)
class EventManager {
    private final Map<String, List<EventListener>> listeners = new HashMap<>();

    public void subscribe(String eventType, EventListener listener) {
        listeners.computeIfAbsent(eventType, k -> new ArrayList<>()).add(listener);
    }

    public void unsubscribe(String eventType, EventListener listener) {
        List<EventListener> list = listeners.get(eventType);
        if (list != null) list.remove(listener);
    }

    public void notifyListeners(String eventType, String data) {
        List<EventListener> list = listeners.get(eventType);
        if (list != null) {
            list.forEach(l -> l.update(eventType, data));
        }
    }
}

// Observers concretos
class EmailAlertListener implements EventListener {
    @Override
    public void update(String eventType, String data) {
        System.out.println("Enviando email sobre [" + eventType + "]: " + data);
    }
}

class LogListener implements EventListener {
    @Override
    public void update(String eventType, String data) {
        System.out.println("Log [" + eventType + "]: " + data);
    }
}

// Uso
// EventManager manager = new EventManager();
// manager.subscribe("save", new EmailAlertListener());
// manager.subscribe("save", new LogListener());
// manager.notifyListeners("save", "archivo.txt");
```

**Cuándo usarlo:** Cuando un cambio en un objeto debe reflejarse en otros sin acoplarlos directamente.

---

### 3.2 Strategy

**Propósito:** Definir una **familia de algoritmos**, encapsular cada uno y hacerlos intercambiables. Permite que el algoritmo varíe independientemente del cliente.

**Problema que resuelve:** Cuando tienes múltiples algoritmos para una tarea y quieres elegir entre ellos en tiempo de ejecución.

**Estructura:**

```
┌──────────────────┐        ┌──────────────────┐
│    Context       │───────▶│    Strategy      │
├──────────────────┤        │   (interfaz)     │
│-strategy         │        ├──────────────────┤
│+setStrategy()    │        │+execute()        │
│+doSomething()    │        └────────┬─────────┘
└──────────────────┘                 │
                          ┌──────────┼──────────┐
                   ┌──────┴───┐ ┌────┴─────┐ ┌──┴──────────┐
                   │StrategyA │ │StrategyB │ │ StrategyC   │
                   └──────────┘ └──────────┘ └─────────────┘
```

**Participantes:**
- `Strategy`: Interfaz común para todos los algoritmos.
- `ConcreteStrategy`: Implementa un algoritmo específico.
- `Context`: Mantiene una referencia a un objeto Strategy y delega el trabajo.

**Ejemplo en Java:**

```java
// Strategy
interface SortStrategy {
    int[] sort(int[] data);
}

class QuickSortStrategy implements SortStrategy {
    @Override
    public int[] sort(int[] data) {
        System.out.println("Ordenando con QuickSort");
        int[] copy = data.clone();
        Arrays.sort(copy); // Simplificado
        return copy;
    }
}

class BubbleSortStrategy implements SortStrategy {
    @Override
    public int[] sort(int[] data) {
        System.out.println("Ordenando con BubbleSort");
        int[] copy = data.clone();
        for (int i = 0; i < copy.length - 1; i++) {
            for (int j = 0; j < copy.length - i - 1; j++) {
                if (copy[j] > copy[j + 1]) {
                    int temp = copy[j];
                    copy[j] = copy[j + 1];
                    copy[j + 1] = temp;
                }
            }
        }
        return copy;
    }
}

// Context
class DataProcessor {
    private SortStrategy strategy;

    public DataProcessor(SortStrategy strategy) {
        this.strategy = strategy;
    }

    public void setStrategy(SortStrategy strategy) {
        this.strategy = strategy;
    }

    public int[] process(int[] data) {
        return strategy.sort(data);
    }
}

// Uso
// DataProcessor processor = new DataProcessor(new QuickSortStrategy());
// processor.process(new int[]{5, 3, 1, 4, 2});
// processor.setStrategy(new BubbleSortStrategy()); // Cambio dinámico
```

**Cuándo usarlo:** Cuando necesitas usar diferentes variantes de un algoritmo y poder cambiarlos dinámicamente.

---

### 3.3 Command

**Propósito:** Encapsular una **solicitud como un objeto**, permitiendo parametrizar clientes con diferentes solicitudes, encolar peticiones o implementar operaciones reversibles.

**Problema que resuelve:** Cuando necesitas parametrizar objetos con operaciones, crear colas de operaciones, o implementar undo/redo.

**Estructura:**

```
┌────────┐     ┌──────────────┐     ┌──────────────┐
│Invoker │────▶│   Command    │     │  Receiver    │
├────────┤     │  (interfaz)  │     ├──────────────┤
│+invoke()│    ├──────────────┤     │+action()     │
└────────┘     │+execute()    │     └──────────────┘
               │+undo()       │             ▲
               └──────┬───────┘             │
                      │                     │
               ┌──────┴───────┐             │
               │ConcreteCommand│────────────┘
               ├──────────────┤
               │-receiver     │
               │+execute()    │
               │+undo()       │
               └──────────────┘
```

**Participantes:**
- `Command`: Interfaz para ejecutar una operación.
- `ConcreteCommand`: Vincula un receiver con una acción.
- `Invoker`: Pide al comando que ejecute la solicitud.
- `Receiver`: Sabe cómo realizar las operaciones.

**Ejemplo en Java:**

```java
// Command
interface Command {
    void execute();
    void undo();
}

// Receiver
class TextEditor {
    private StringBuilder content = new StringBuilder();

    public void addText(String text) { content.append(text); }
    public void removeText(int length) {
        content.delete(content.length() - length, content.length());
    }
    public String getContent() { return content.toString(); }
}

// Concrete Command
class AddTextCommand implements Command {
    private final TextEditor editor;
    private final String text;

    public AddTextCommand(TextEditor editor, String text) {
        this.editor = editor;
        this.text = text;
    }

    @Override
    public void execute() { editor.addText(text); }

    @Override
    public void undo() { editor.removeText(text.length()); }
}

// Invoker (historial de comandos)
class CommandHistory {
    private final Stack<Command> history = new Stack<>();

    public void executeCommand(Command command) {
        command.execute();
        history.push(command);
    }

    public void undoLast() {
        if (!history.isEmpty()) {
            history.pop().undo();
        }
    }
}

// Uso
// TextEditor editor = new TextEditor();
// CommandHistory history = new CommandHistory();
// history.executeCommand(new AddTextCommand(editor, "Hola "));
// history.executeCommand(new AddTextCommand(editor, "Mundo"));
// System.out.println(editor.getContent()); // "Hola Mundo"
// history.undoLast();
// System.out.println(editor.getContent()); // "Hola "
```

**Cuándo usarlo:** Cuando necesitas undo/redo, colas de operaciones, o desacoplar el objeto que invoca una operación del que la realiza.

---

### 3.4 State

**Propósito:** Permitir que un objeto **altere su comportamiento** cuando cambia su estado interno. El objeto parecerá haber cambiado de clase.

**Problema que resuelve:** Cuando el comportamiento de un objeto depende de su estado y debe cambiar en tiempo de ejecución.

**Estructura:**

```
┌──────────────────┐        ┌──────────────┐
│    Context       │───────▶│    State     │
├──────────────────┤        │  (interfaz)  │
│-state: State     │        ├──────────────┤
│+request()        │        │+handle()     │
│+changeState()    │        └──────┬───────┘
└──────────────────┘               │
                          ┌────────┼────────┐
                   ┌──────┴──┐  ┌──┴──────┐ ┌┴──────────┐
                   │ StateA  │  │ StateB  │ │  StateC   │
                   └─────────┘  └─────────┘ └───────────┘
```

**Participantes:**
- `Context`: Mantiene una instancia de `State` que define el comportamiento actual.
- `State`: Interfaz para el comportamiento asociado a un estado del contexto.
- `ConcreteState`: Cada subclase implementa el comportamiento de un estado específico.

**Ejemplo en Java:**

```java
// State
interface OrderState {
    void next(Order order);
    void previous(Order order);
    String getStatus();
}

class PendingState implements OrderState {
    @Override
    public void next(Order order) { order.setState(new ProcessingState()); }
    @Override
    public void previous(Order order) {
        System.out.println("Ya estás en el estado inicial");
    }
    @Override
    public String getStatus() { return "PENDIENTE"; }
}

class ProcessingState implements OrderState {
    @Override
    public void next(Order order) { order.setState(new ShippedState()); }
    @Override
    public void previous(Order order) { order.setState(new PendingState()); }
    @Override
    public String getStatus() { return "EN PROCESO"; }
}

class ShippedState implements OrderState {
    @Override
    public void next(Order order) { order.setState(new DeliveredState()); }
    @Override
    public void previous(Order order) { order.setState(new ProcessingState()); }
    @Override
    public String getStatus() { return "ENVIADO"; }
}

class DeliveredState implements OrderState {
    @Override
    public void next(Order order) {
        System.out.println("Pedido ya entregado, no hay siguiente paso");
    }
    @Override
    public void previous(Order order) { order.setState(new ShippedState()); }
    @Override
    public String getStatus() { return "ENTREGADO"; }
}

// Context
class Order {
    private OrderState state = new PendingState();

    public void setState(OrderState state) { this.state = state; }
    public void nextStep() { state.next(this); }
    public void previousStep() { state.previous(this); }
    public String getStatus() { return state.getStatus(); }
}

// Uso
// Order order = new Order();
// order.getStatus();   // "PENDIENTE"
// order.nextStep();
// order.getStatus();   // "EN PROCESO"
```

**Cuándo usarlo:** Cuando un objeto tiene un comportamiento que cambia drásticamente según su estado interno.

---

### 3.5 Chain of Responsibility

**Propósito:** Evitar acoplar al emisor de una solicitud con su receptor, dando a **más de un objeto la posibilidad de manejar** la solicitud. Los objetos receptores se encadenan y la solicitud se pasa a lo largo de la cadena.

**Estructura:**

```
┌────────┐     ┌──────────────────┐
│ Client │────▶│    Handler       │
└────────┘     │   (interfaz)     │
               ├──────────────────┤
               │-nextHandler      │────▶ (siguiente Handler)
               │+handle(request)  │
               └────────┬─────────┘
                        │
            ┌───────────┼───────────┐
     ┌──────┴──────┐ ┌──┴──────┐ ┌─┴───────────┐
     │ HandlerA   │ │HandlerB │ │  HandlerC   │
     └────────────┘ └─────────┘ └─────────────┘
```

**Ejemplo en Java:**

```java
// Ticket de soporte
class SupportTicket {
    private final String severity; // "low", "medium", "high"
    private final String issue;

    public SupportTicket(String severity, String issue) {
        this.severity = severity;
        this.issue = issue;
    }

    public String getSeverity() { return severity; }
    public String getIssue() { return issue; }
}

// Handler abstracto
abstract class SupportHandler {
    private SupportHandler next;

    public SupportHandler setNext(SupportHandler handler) {
        this.next = handler;
        return handler; // Permite encadenamiento
    }

    public String handle(SupportTicket ticket) {
        if (next != null) {
            return next.handle(ticket);
        }
        return "No se pudo resolver: " + ticket.getIssue();
    }
}

class Level1Support extends SupportHandler {
    @Override
    public String handle(SupportTicket ticket) {
        if ("low".equals(ticket.getSeverity())) {
            return "Resuelto por Nivel 1: " + ticket.getIssue();
        }
        return super.handle(ticket);
    }
}

class Level2Support extends SupportHandler {
    @Override
    public String handle(SupportTicket ticket) {
        if ("medium".equals(ticket.getSeverity())) {
            return "Resuelto por Nivel 2: " + ticket.getIssue();
        }
        return super.handle(ticket);
    }
}

class Level3Support extends SupportHandler {
    @Override
    public String handle(SupportTicket ticket) {
        if ("high".equals(ticket.getSeverity())) {
            return "Resuelto por Nivel 3 (escalado): " + ticket.getIssue();
        }
        return super.handle(ticket);
    }
}

// Uso: Cadena L1 → L2 → L3
// SupportHandler l1 = new Level1Support();
// l1.setNext(new Level2Support()).setNext(new Level3Support());
// l1.handle(new SupportTicket("high", "Servidor caído"));
```

**Cuándo usarlo:** Cuando más de un objeto puede manejar una solicitud y el manejador no se conoce a priori.

---

### 3.6 Iterator

**Propósito:** Proporcionar una forma de **acceder secuencialmente** a los elementos de un objeto agregado sin exponer su representación interna.

**Estructura:**

```
┌──────────────────┐        ┌──────────────────┐
│   Aggregate      │───────▶│    Iterator      │
│  (interfaz)      │        │   (interfaz)     │
├──────────────────┤        ├──────────────────┤
│+createIterator() │        │+hasNext(): bool  │
└──────────────────┘        │+next(): Element  │
                            └──────────────────┘
```

**Ejemplo en Java:**

```java
// Iterador personalizado
class AlphabetIterator implements Iterator<Character> {
    private char current;
    private final char end;

    public AlphabetIterator(char start, char end) {
        this.current = start;
        this.end = end;
    }

    @Override
    public boolean hasNext() {
        return current <= end;
    }

    @Override
    public Character next() {
        if (!hasNext()) throw new NoSuchElementException();
        return current++;
    }
}

// Colección iterable
class AlphabetRange implements Iterable<Character> {
    private final char start;
    private final char end;

    public AlphabetRange(char start, char end) {
        this.start = start;
        this.end = end;
    }

    @Override
    public Iterator<Character> iterator() {
        return new AlphabetIterator(start, end);
    }
}

// Uso con for-each
// for (char letter : new AlphabetRange('a', 'e')) {
//     System.out.println(letter); // a, b, c, d, e
// }
```

**Cuándo usarlo:** Cuando necesitas recorrer una colección sin exponer su estructura interna.

---

### 3.7 Mediator

**Propósito:** Definir un objeto que **encapsule cómo interactúan** un conjunto de objetos. Promueve el bajo acoplamiento evitando que los objetos se refieran entre sí explícitamente.

**Estructura:**

```
┌──────────────────┐        ┌──────────────┐
│    Mediator      │◀───────│  Colleague   │
│   (interfaz)     │        │ (interfaz)   │
├──────────────────┤        └──────┬───────┘
│+notify(sender,   │               │
│        event)    │     ┌─────────┼─────────┐
└──────────────────┘     │         │         │
                      ColleagueA ColleagueB ColleagueC
```

**Ejemplo en Java:**

```java
// Mediador
interface ChatMediator {
    void sendMessage(String message, User sender, String receiverName);
    void register(User user);
}

class ChatRoom implements ChatMediator {
    private final Map<String, User> users = new HashMap<>();

    @Override
    public void register(User user) {
        users.put(user.getName(), user);
    }

    @Override
    public void sendMessage(String message, User sender, String receiverName) {
        User receiver = users.get(receiverName);
        if (receiver != null) {
            receiver.receive(message, sender.getName());
        }
    }
}

// Colleague
class User {
    private final String name;
    private final ChatMediator chatRoom;

    public User(String name, ChatMediator chatRoom) {
        this.name = name;
        this.chatRoom = chatRoom;
        chatRoom.register(this);
    }

    public String getName() { return name; }

    public void send(String message, String to) {
        chatRoom.sendMessage(message, this, to);
    }

    public void receive(String message, String fromName) {
        System.out.println(name + " recibió de " + fromName + ": " + message);
    }
}

// Uso
// ChatMediator room = new ChatRoom();
// User alice = new User("Alice", room);
// User bob = new User("Bob", room);
// alice.send("¡Hola!", "Bob"); // Bob recibió de Alice: ¡Hola!
```

**Cuándo usarlo:** Cuando un conjunto de objetos se comunica de formas complejas y las interdependencias son difíciles de entender.

---

### 3.8 Memento

**Propósito:** Capturar y externalizar el **estado interno de un objeto** sin violar el encapsulamiento, para poder restaurarlo más tarde.

**Estructura:**

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Originator  │────▶│   Memento    │◀────│  Caretaker   │
├──────────────┤     ├──────────────┤     ├──────────────┤
│-state        │     │-state        │     │-mementos[]   │
│+save(): Memento│   └──────────────┘     │+undo()       │
│+restore(m)   │                          └──────────────┘
└──────────────┘
```

**Ejemplo en Java:**

```java
// Memento
class EditorMemento {
    private final String content;
    private final int cursorPosition;

    public EditorMemento(String content, int cursorPosition) {
        this.content = content;
        this.cursorPosition = cursorPosition;
    }

    public String getContent() { return content; }
    public int getCursorPosition() { return cursorPosition; }
}

// Originator
class TextEditor {
    private StringBuilder content = new StringBuilder();
    private int cursor = 0;

    public EditorMemento save() {
        return new EditorMemento(content.toString(), cursor);
    }

    public void restore(EditorMemento memento) {
        this.content = new StringBuilder(memento.getContent());
        this.cursor = memento.getCursorPosition();
    }

    public void write(String text) {
        content.append(text);
        cursor = content.length();
    }

    public String getContent() { return content.toString(); }
}

// Caretaker
class EditorHistory {
    private final Stack<EditorMemento> history = new Stack<>();

    public void save(TextEditor editor) {
        history.push(editor.save());
    }

    public void undo(TextEditor editor) {
        if (!history.isEmpty()) {
            editor.restore(history.pop());
        }
    }
}

// Uso
// TextEditor editor = new TextEditor();
// EditorHistory history = new EditorHistory();
// history.save(editor);
// editor.write("Hola ");
// history.save(editor);
// editor.write("Mundo");
// System.out.println(editor.getContent()); // "Hola Mundo"
// history.undo(editor);
// System.out.println(editor.getContent()); // "Hola "
```

**Cuándo usarlo:** Cuando necesitas implementar snapshots o undo sin exponer los detalles de implementación del objeto.

---

### 3.9 Template Method

**Propósito:** Definir el **esqueleto de un algoritmo** en una operación, difiriendo algunos pasos a las subclases.

**Estructura:**

```
┌─────────────────────────┐
│    AbstractClass        │
├─────────────────────────┤
│+templateMethod()        │ → Llama step1(), step2(), step3()
│+step1()                 │ (concreto o abstracto)
│+step2()                 │ (abstracto - subclases implementan)
│+step3()                 │
└────────────┬────────────┘
             │
┌────────────┴────────────┐
│    ConcreteClass        │
├─────────────────────────┤
│+step2()                 │ → Implementación específica
└─────────────────────────┘
```

**Ejemplo en Java:**

```java
// Template
abstract class DataMiner {

    // Template method: define el esqueleto del algoritmo
    public final void mine(String path) {
        Object file = openFile(path);
        String rawData = extractData(file);
        Object data = parseData(rawData);
        Object analysis = analyzeData(data);
        sendReport(analysis);
    }

    protected abstract Object openFile(String path);
    protected abstract String extractData(Object file);
    protected abstract Object parseData(String rawData);

    // Hook: comportamiento por defecto que puede sobreescribirse
    protected Object analyzeData(Object data) {
        return data;
    }

    protected void sendReport(Object analysis) {
        System.out.println("Reporte: " + analysis);
    }
}

class CSVDataMiner extends DataMiner {
    @Override
    protected Object openFile(String path) {
        System.out.println("Abriendo CSV: " + path);
        return path;
    }

    @Override
    protected String extractData(Object file) {
        return "nombre,edad,ciudad";
    }

    @Override
    protected Object parseData(String rawData) {
        return rawData.split(",");
    }
}

class JSONDataMiner extends DataMiner {
    @Override
    protected Object openFile(String path) {
        System.out.println("Abriendo JSON: " + path);
        return path;
    }

    @Override
    protected String extractData(Object file) {
        return "{\"nombre\": \"Juan\"}";
    }

    @Override
    protected Object parseData(String rawData) {
        return rawData; // Simplificado
    }
}

// Uso
// DataMiner miner = new CSVDataMiner();
// miner.mine("datos.csv");
```

**Cuándo usarlo:** Cuando varias clases tienen algoritmos similares con pequeñas diferencias en algunos pasos.

---

### 3.10 Visitor

**Propósito:** Representar una **operación que se ejecuta sobre los elementos** de una estructura de objetos. Permite definir nuevas operaciones sin cambiar las clases de los elementos.

**Estructura:**

```
┌──────────────────┐         ┌──────────────────┐
│    Visitor       │         │    Element       │
│   (interfaz)     │         │   (interfaz)     │
├──────────────────┤         ├──────────────────┤
│+visitElementA()  │◀────────│+accept(Visitor)  │
│+visitElementB()  │         └────────┬─────────┘
└──────────────────┘                  │
                             ┌────────┴─────────┐
                             │ ConcreteElement   │
                             ├──────────────────┤
                             │+accept(v: Visitor)│
                             │  → v.visitThis() │
                             └──────────────────┘
```

**Ejemplo en Java:**

```java
// Visitor
interface ShapeVisitor {
    double visitCircle(Circle circle);
    double visitRectangle(Rectangle rectangle);
}

class AreaCalculator implements ShapeVisitor {
    @Override
    public double visitCircle(Circle circle) {
        return Math.PI * circle.getRadius() * circle.getRadius();
    }

    @Override
    public double visitRectangle(Rectangle rectangle) {
        return rectangle.getWidth() * rectangle.getHeight();
    }
}

class PerimeterCalculator implements ShapeVisitor {
    @Override
    public double visitCircle(Circle circle) {
        return 2 * Math.PI * circle.getRadius();
    }

    @Override
    public double visitRectangle(Rectangle rectangle) {
        return 2 * (rectangle.getWidth() + rectangle.getHeight());
    }
}

// Elements
interface Shape {
    double accept(ShapeVisitor visitor);
}

class Circle implements Shape {
    private final double radius;

    public Circle(double radius) { this.radius = radius; }
    public double getRadius() { return radius; }

    @Override
    public double accept(ShapeVisitor visitor) {
        return visitor.visitCircle(this);
    }
}

class Rectangle implements Shape {
    private final double width, height;

    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    public double getWidth() { return width; }
    public double getHeight() { return height; }

    @Override
    public double accept(ShapeVisitor visitor) {
        return visitor.visitRectangle(this);
    }
}

// Uso
// Shape circle = new Circle(5);
// ShapeVisitor areaCalc = new AreaCalculator();
// double area = circle.accept(areaCalc); // ≈ 78.54
```

**Cuándo usarlo:** Cuando necesitas realizar operaciones sobre objetos de una estructura compleja y quieres evitar "contaminar" sus clases con estas operaciones.

---

### 3.11 Interpreter

**Propósito:** Dado un lenguaje, definir una **representación de su gramática** junto con un intérprete que usa esa representación para interpretar sentencias del lenguaje.

**Estructura:**

```
┌──────────────────────┐
│  AbstractExpression   │
├──────────────────────┤
│+interpret(context)    │
└──────────┬───────────┘
           │
  ┌────────┴────────────┐
  │                     │
┌─┴──────────────┐  ┌──┴─────────────────┐
│TerminalExpr.   │  │NonterminalExpr.    │
│(hoja: literal) │  │(compuesto: regla)  │
└────────────────┘  └────────────────────┘
```

**Ejemplo en Java:**

```java
// Expresión abstracta
interface Expression {
    int interpret();
}

// Terminal: número
class NumberExpression implements Expression {
    private final int value;

    public NumberExpression(int value) { this.value = value; }

    @Override
    public int interpret() { return value; }
}

// No-terminal: suma
class AddExpression implements Expression {
    private final Expression left;
    private final Expression right;

    public AddExpression(Expression left, Expression right) {
        this.left = left;
        this.right = right;
    }

    @Override
    public int interpret() {
        return left.interpret() + right.interpret();
    }
}

// No-terminal: multiplicación
class MultiplyExpression implements Expression {
    private final Expression left;
    private final Expression right;

    public MultiplyExpression(Expression left, Expression right) {
        this.left = left;
        this.right = right;
    }

    @Override
    public int interpret() {
        return left.interpret() * right.interpret();
    }
}

// Uso: representar (3 + 5) * 2
// Expression expr = new MultiplyExpression(
//     new AddExpression(new NumberExpression(3), new NumberExpression(5)),
//     new NumberExpression(2)
// );
// System.out.println(expr.interpret()); // 16
```

**Cuándo usarlo:** Cuando tienes un lenguaje simple que interpretar y puedes representar sus sentencias como árboles de sintaxis abstracta.

---

## 📊 Resumen Comparativo

### Patrones Creacionales

| Patrón | Propósito principal | Palabra clave |
|--------|-------------------|---------------|
| Singleton | Una sola instancia global | **Único** |
| Factory Method | Delegar creación a subclases | **Delegación** |
| Abstract Factory | Familias de objetos relacionados | **Familias** |
| Builder | Construcción paso a paso | **Paso a paso** |
| Prototype | Clonar objetos existentes | **Clonar** |

### Patrones Estructurales

| Patrón | Propósito principal | Palabra clave |
|--------|-------------------|---------------|
| Adapter | Compatibilizar interfaces | **Compatibilidad** |
| Bridge | Separar abstracción de implementación | **Desacoplar** |
| Composite | Jerarquías parte-todo | **Árbol** |
| Decorator | Añadir responsabilidades dinámicamente | **Envolver** |
| Facade | Interfaz simplificada | **Simplificar** |
| Flyweight | Compartir objetos para ahorrar memoria | **Compartir** |
| Proxy | Controlar acceso a un objeto | **Intermediario** |

### Patrones de Comportamiento

| Patrón | Propósito principal | Palabra clave |
|--------|-------------------|---------------|
| Observer | Notificar cambios a múltiples objetos | **Notificación** |
| Strategy | Intercambiar algoritmos | **Intercambiar** |
| Command | Encapsular solicitudes como objetos | **Encapsular** |
| State | Cambiar comportamiento según estado | **Estado** |
| Chain of Responsibility | Pasar solicitud por una cadena | **Cadena** |
| Iterator | Recorrer colecciones | **Recorrer** |
| Mediator | Centralizar comunicación | **Central** |
| Memento | Guardar/restaurar estado | **Snapshot** |
| Template Method | Esqueleto de algoritmo | **Esqueleto** |
| Visitor | Operaciones sobre estructuras | **Operar** |
| Interpreter | Interpretar gramáticas | **Gramática** |

---

## 📚 Referencias

- Gamma, E., Helm, R., Johnson, R., & Vlissides, J. (1994). *Design Patterns: Elements of Reusable Object-Oriented Software*. Addison-Wesley.
- [Refactoring Guru - Design Patterns](https://refactoring.guru/design-patterns)
- [Source Making - Design Patterns](https://sourcemaking.com/design_patterns)

---

> **Nota:** Todos los ejemplos de código están escritos en **Java**. Los patrones son aplicables a cualquier lenguaje orientado a objetos (Python, C#, C++, TypeScript, etc.).
