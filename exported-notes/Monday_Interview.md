# Monday Interview
### SQL
- __Inner Join:__ Returns records from two or more tables with commonalities. `SELECT * FROM APPLE A INNER JOIN BANANA B ON A.ID = B.ID` Would return records that match those records in A and B.
- __Outer Join:__ Returns records from two or more tables with commonalities and records which don't match. 
  - __Left Outer Join__: Returns all data from Table 1 + matching data from Table 2.
  - __Right Outer Join__: Returns all data from Table 2 + matching data from Table 1.
  - __Full Outer Join__: Returns all data from Table 1 and Table 2.
- __Stored Procedures:__ SQL procedures that can be called at a later time and executed. Parameters can be passed in to be used by the stored procedures.
  - *Stored procedures were just implemented in PostgreSQL 11. Before they were called functions*
  - ```SQL
    --This is MySQL
    CREATE PROCEDURE SIMPLE_PROC(OUT param_1 INT)
    BEGIN
      SELECT COUNT(*) INTO PARAM_1 FROM T;
    END
    ```
  - The previous uses an `OUT` parameter which gets referenced by the caller. So calling `CALL SIMPLE_PROC(@a)` would set `@a` to whatever the SQL statement sets.
- `INTO` copies data from one table to another.
- __Subqueries:__ Queries embedded into a SQL query. Subqueries can have only one column defined.
  - `SELECT * FROM TABLE WHERE TABLE.ID NOT IN (SELECT TABLE FROM RESERVATION)`
- Postgres date functions:
  - `current_date`
  - `current_time`
  - `now()`
  - `age(timestamp, timestamp)` - Subtract two dates
  - `age(timestamp)` - Subtract from current date, midnight



### OOP Principles
- __Polymorphism:__ "One name, many forms." Allows us to write code for a superclass but lets us use code for its subclasses. Static polymorphism is achieved via method overloading and dynamic polymorphism is achieved via method overriding. 
  - Interfaces allows the definition of contracts for classes in code. 
  - __Method Overloading:__ Using the same name for a method with different sets of parameters.
  - __Method Overriding:__ Using a superclass' method with a subclass' implementation.
- __Encapsulation:__ Hiding data implementation by exposing public methods. I.e. make your class variables private and expose them via methods such as getters and setters.
- __Abstraction:__ Using `abstract` classes/interfaces to represent the intention of that class rather than the actual implementation. This is useful when separate classes behave similarily or perform similar functions, abstract methods are overridden. 
- __Inheritence:__ Classes are able to inherit methods and variables from other classes using the `is-a` or `has-a` relationship. 
- __Inversion of Control:__ Inverting control--instead of us having control over objects or other parts of the program we delegate that to a framework (Spring). Example would be Dependency Injection, where dependency management is delegated to the framework.
- __Dependency Injection:__ Object dependencies are pretty straight forward: Object A requires Object B in order to be constructed
  ```JAVA
  public class A {

    private B b;

    public A () {

      this.b = new BImpl();
      // Class A is now depended on class B
    }
  }

  ```
  With dependency injection, these dependencies are "injected" by the framework itself. When the    dependencies are polymorphic, the implementation of the depenencies does not have to be specified.

### Spring Annotations
- `@Autowired` - Wires in dependency 
- `@Component` - General Spring component, these classes are considered candidates for detection when using classpath-scanning and annotation-based configuration.
- `@Service` - Indicates a service, more specialized verision of `@Component`. Services relate to domain concepts but are not a natural part of the entity. Services are generally exposed as interfaces to define a contract.
- `@RequestMapping` - URI mapping which will be used to route to the annotated method:
  - @RequestParam - Parameter found in the URI (e.g. `/api/program?id=123`)
  - @PathVariable - Indicates a variable is found in the path itself. (e.g. `api/program/123`)

# Java and Computer Science

### Garbage collector: 
 
- In-use objects maintain pointers to them. Unused objects no longer have pointers associated with them.
- Step 1: Mark unused objects.
- Step 2: Normal deletion: delete the marked objects
- Step 2a: Deletion with compacting. Compact referenced objects to make memory allocation easier.
- Hotspot GC uses generational heap separation: 
  - Young
  - Old
  - Permanent

### Heap vs Stack

  - Heap is dynamic memory--no specific amount of heap allocated. Objects created are saved in heap memory, picked up by garbage collectors once they're not used. More inefficient than stack memory due to the allocation and the use of pointers. 
    - Objects are stored in heap memory, along with their methods and instance variables.
  - Stack memory is static. Stack memory is faster because it's access and deallocation is LIFO. When a function exits, all variables declared by that function are deallocated.
    - Local and static variables are stored in stack memory
  - __Each thread in Java has a stack, all stacks share a heap__

### Java Concurrency
- __Deadlock:__ Two or more threads are stuck waiting for eachother and thus stuck forever.
- `Thread` uses `Runnable` objects to execute `run()`
- __`Executor`s:__ Encapsulate `Runnable` and `Thread` objects. The `Executor` interface is implemented by classes such as `ThreadPoolExecutor` which executes the supplied threads.