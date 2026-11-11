# UML Diagrams — Practice

These prompts are meant to be answered as if in a design interview. The goal is not just to draw symbols correctly, but to explain what question the diagram is answering and what details are intentionally omitted.

## 1) Class diagram — library borrowing

### Question

Draw a class diagram for a library member borrowing a copy of a book. Mark which objects have independent identity.

### Model answer

```text
+-------------------+       borrows       +-------------------+
| LibraryMember     | ------------------>| Loan              |
|-------------------|                    |-------------------|
| memberId: String  |                    | loanId: String    |
| name: String      |                    | issuedOn: Date    |
+-------------------+                    | dueDate: Date     |
       ^                                | return()          |
       |                                +-------------------+
       |                                         ^
       |                                         |
       |                                         |
+-------------------+                         +-------------------+
| BookCopy          |                         | Book              |
|-------------------|                         |-------------------|
| copyId: String    |                         | isbn: String      |
| status: Status    |                         | title: String     |
| isAvailable()     |                         | author: String    |
+-------------------+                         +-------------------+
```

Explanation:

- `LibraryMember` is a person with identity and independent lifecycle.
- `Book` is the catalog entry and has identity as a title/edition, not a copy.
- `BookCopy` is a physical instance that can be checked out, returned, or lost.
- `Loan` is the association between a member and a specific copy during a borrowing period.

The important modeling decision is distinguishing the abstract book from the physical copy. In a library, a single book may have many copies, and each copy can be borrowed independently.

## 2) Sequence diagram — placing an order

### Question

Draw a sequence diagram for placing an order with inventory validation and payment authorization. Include one failure path.

### Model answer

```text
Customer -> OrderService: submitOrder(cart)
OrderService -> InventoryService: reserveItems(cart)
alt inventory available
    InventoryService --> OrderService: reserved
    OrderService -> PaymentGateway: authorize(payment)
    alt payment approved
        PaymentGateway --> OrderService: approved
        OrderService -> OrderRepository: save(order)
        OrderRepository --> OrderService: saved
        OrderService --> Customer: order confirmed
    else payment declined
        PaymentGateway --> OrderService: declined
        OrderService --> Customer: payment failed
    end
else inventory unavailable
    InventoryService --> OrderService: outOfStock
    OrderService --> Customer: cannot complete order
end
```

Explanation:

This sequence diagram is useful because it reveals ordering and failure-handling conditions. A customer is not required to know the interruption logic. The system checks stock first, then authorizes payment, then saves the order. The failure path ensures we model both the “unexpected stock issue” and the “payment rejected” branch.

## 3) State diagram — traffic light lifecycle

### Question

Model a traffic light lifecycle using a state diagram. What makes an invalid transition invalid?

### Model answer

```text
[Red] --timer expired--> [Green]
[Green] --timer expired--> [Yellow]
[Yellow] --timer expired--> [Red]
```

Invalid transitions are invalid because the system does not allow them under the current lifecycle rules. For example, a traffic light cannot go directly from `Red` to `Yellow` without a green phase unless the system intentionally models a special override. A guard or state rule defines these constraints.

The key idea is that state diagrams explain legal transitions. If a transition is not modeled, or is blocked by a guard, it is not permitted.

## 4) Activity vs sequence diagram — approval workflow

### Question

Choose between an activity diagram and sequence diagram for a multi-step approval workflow. Explain what each would reveal.

### Model answer

- Use an activity diagram when the main question is workflow logic: who approves next, what happens after each decision, and whether parallel approvals occur.
- Use a sequence diagram when the main question is interaction flow between participants and the order of messages.

Example activity flow:

```text
Submit request
   |
   v
Manager review?
   |-- reject --> Notify employee
   |
   |-- approve --> Finance review?
                     |-- reject --> Notify employee
                     |
                     |-- approve --> Finalize request
```

This reveals the business logic and decision points. A sequence diagram would instead show `Employee -> Manager -> Finance` messages and the outcome of each message.

## 5) Use case vs class diagram — online marketplace

### Question

You are asked to model a marketplace. Which diagram is more useful first: a use case diagram or class diagram? Why?

### Model answer

Use a use case diagram first if the main uncertainty is scope: who are the actors, and what major goals should the system support? For example: `Buyer`, `Seller`, `Admin`, `SearchCatalog`, `PlaceOrder`, `ManageInventory`.

Then move to a class diagram if the team needs to decide how entities relate, such as `Buyer`, `Order`, `Product`, `Seller`, and `Payment`.

A use case diagram answers “what capabilities are in scope?” while a class diagram answers “what types and relationships are in the model?”

## 6) Object diagram — real instance graph

### Question

Model a specific moment in time: one customer has placed one order with two items. Show which details are instance-level and which are type-level.

### Model answer

```text
customer: Customer
  id = "C-42"
  name = "Priya"

order: Order
  orderId = "O-104"
  status = "Pending"

item1: OrderLine
  sku = "A-1"
  quantity = 2

item2: OrderLine
  sku = "B-7"
  quantity = 1
```

Relationships:

- customer -> order
- order -> item1
- order -> item2

This is an object diagram because it shows concrete instances and their values, not general classes. It is useful when a real example is confusing or a subtle relationship needs to be illustrated.

## 7) Component diagram — payments and inventory

### Question

Describe a component diagram for a checkout flow involving payment and inventory subsystems.

### Model answer

```text
+-----------------+        depends on        +----------------------+
| Checkout API    | -----------------------> | Inventory Service    |
+-----------------+                          +----------------------+
        |
        | provides
        v
+-----------------+        depends on        +----------------------+
| Order Service   | -----------------------> | Payment Gateway      |
+-----------------+                          +----------------------+
```

This diagram makes the architectural boundary clear: `Checkout API` relies on `Order Service`, which depends on `Inventory Service` and `Payment Gateway`. The point is to show modules and contracts, not the exact method calls within one scenario.

## 8) Interview-time choice — turn-based game

### Question

With five minutes left in an interview, choose one diagram for a turn-based game feature and justify what you omit.

### Model answer

I would choose a state diagram for a turn system, because the essential question is whether a player can move from `Ready` to `Thinking` to `ActionResolved` and whether invalid transitions are prevented.

I would omit the internal implementation details of the renderer, animation subsystem, or every UI event. Those are not needed to answer the lifecycle question. The diagram should focus on legal states and transitions, because that is where the design risk lives.

## Practice checklist

Before finishing any UML answer in an interview, check that you can explain:

- what the diagram is trying to answer
- why this diagram is better than alternatives
- which details are included and which are omitted
- what invalid or ambiguous transitions mean
- how the model changes if the requirements change
