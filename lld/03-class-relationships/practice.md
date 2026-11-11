# Class Relationships — Practice

These questions are designed to train your decision-making around ownership, lifetime, and substitutability. For each scenario, state the likely relationship first, then explain the domain assumption behind it.

## 1. Order and LineItem

For `Order` and `LineItem`, decide whether composition fits. What changes if line items are reusable catalog products?

### Answer

Composition fits when an order owns its line items and the line items do not have a meaningful independent lifecycle outside the order.

```java
class Order {
    private List<LineItem> lineItems = new ArrayList<>();

    void addItem(LineItem item) {
        lineItems.add(item);
    }
}

class LineItem {
    private String sku;
    private int quantity;
}
```

If line items are reusable catalog products, then the part is not owned by the order in the same way. Instead, the order may contain references to catalog entries or copies of product data. That turns the relationship toward aggregation or an association plus value-object modeling.

The key design question is: does the line item live and die with the order or does it exist independently in the catalog?

## 2. ReportGenerator and Clock

Model a `ReportGenerator` that uses a `Clock` only while generating one report. Is that a dependency or association? What if it stores the clock?

### Answer

If the clock is only passed in as a method argument or local dependency, this is a dependency.

```java
class ReportGenerator {
    void generateReport(Clock clock, User user) {
        LocalDate today = clock.today();
        // generate report
    }
}
```

If the generator stores `Clock` as a field and uses it across multiple operations, it becomes an association or a dependency injected as a collaborator. The distinction is based on lifetime and whether the object permanently holds the reference.

A strong design often uses dependency injection:

```java
class ReportGenerator {
    private final Clock clock;

    ReportGenerator(Clock clock) {
        this.clock = clock;
    }
}
```

This is still not necessarily composition; it is a collaborator relationship. The decision depends on whether the clock is an essential part of the generator's state or simply a service it consults.

## 3. CsvExporter and Exporter

Draw a relationship between `CsvExporter` and `Exporter`. Identify the arrow direction if the former implements the latter.

### Answer

`CsvExporter` implements `Exporter`.

```java
interface Exporter {
    void export(String path);
}

class CsvExporter implements Exporter {
    public void export(String path) {
        // CSV logic
    }
}
```

In UML, the dashed arrow points from the implementing class to the interface or abstract contract. The concrete class realizes the contract. In code, callers usually depend on `Exporter`, not on `CsvExporter` directly.

This supports substitutability: a `CsvExporter`, `JsonExporter`, or `PdfExporter` can all satisfy `Exporter`.

## 4. Playlist and tracks

A playlist contains tracks. Describe two plausible ownership models and the product assumptions behind each.

### Answer

Model A: Composition

```java
class Playlist {
    private List<Track> tracks;
}
```

This assumes the playlist owns the track instances. If the playlist is deleted, the tracks are deleted too. It is a strong ownership model.

Model B: Aggregation or association

```java
class Playlist {
    private List<TrackRef> trackRefs;
}
```

This assumes tracks are shared library items or songs in a catalog. The same track may belong to multiple playlists, and deleting a playlist does not remove the song from the music library.

The deciding factor is whether tracks have independent identity and lifecycle.

## 5. Square extends Rectangle

Review a proposed `Square extends Rectangle`. Which behavioral contracts could make the inheritance misleading?

### Answer

The inheritance is misleading if `Square` cannot faithfully satisfy the contract of `Rectangle`.

```java
class Rectangle {
    int width;
    int height;

    void setWidth(int w) { width = w; }
    void setHeight(int h) { height = h; }
    int area() { return width * height; }
}
```

A `Square` must maintain equal width and height. If the base class contract allows changing one dimension without changing the other, then `Square` breaks substitutability.

This violates the rule:

- A `Square` is not always safe to substitute for a `Rectangle`.
- The subclass should not strengthen or alter a contract in an incompatible way.

A better design is often:

```java
interface Shape {
    int area();
}

class Rectangle implements Shape { ... }
class Square implements Shape { ... }
```

This preserves the real geometry logic without pretending a square is a valid subtype of every rectangle behavior.

## 6. School and Students

A `School` references students. Is this association, aggregation, or composition? What assumptions change the answer?

### Answer

The relationship is usually association or aggregation, not composition, unless the domain says students are only meaningful inside that school.

If the school has an enrollment roster but students are independent people with their own records, the relationship is aggregation or association.

If a business rule says the student object is created only for the school and cannot exist elsewhere, composition may fit.

The answer depends on the domain boundary: do students exist independently of the school?

## 7. Logger and FileLogger

A `Service` depends on a `Logger`. What design choice makes the service more maintainable?

### Answer

Use an abstraction and inject the concrete implementation.

```java
interface Logger {
    void log(String message);
}

class FileLogger implements Logger {
    public void log(String message) {
        // write to file
    }
}
```

The service depends on the interface, not the file implementation. This makes it easy to replace with a database logger or a test logger without changing the service's business logic.

This is a standard example of dependency plus realization: the service depends on the contract, and the concrete logger realizes it.

## 8. Car and Engine

When is `Car` owning `Engine` composition, and when is it just an association?

### Answer

Composition fits when the engine is not a reusable independent object in the system and the car's lifecycle owns it.

```java
class Car {
    private final Engine engine;
    Car() { engine = new Engine(); }
}
```

Association or aggregation fits when the engine can be replaced, reused, or inventoried independently, such as a manufacturer managing engines as separate components.

The difference is not in the syntax alone. It is in the business semantics: does the engine exist independently, or is it a child object of the car?

## 9. Team and Player

A team includes players. Is this composition or aggregation?

### Answer

It depends on the business rules.

- If a player is a transient member of a team and can be transferred, traded, or inactive while still existing as a person object, then aggregation is a better fit.
- If a player is created as part of a team and cannot exist independently in the modeled system, composition is possible.

The design must match the actual product assumptions. If those assumptions are not clear, the modeling is weak.

## 10. Practice summary

When you answer these questions in interviews, the best response is not only “this is composition” or “this is association.” You should explain:

- the lifetime of the parts
- whether the object can exist independently
- whether the relationship is temporary or permanent
- whether the contract is a real subtype relationship

That shows you are reasoning about design, not just naming UML arrows.
