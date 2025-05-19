# Hibernate Cascading Operations

Cascading is a feature in Hibernate that allows operations performed on a parent entity to cascade or propagate to associated child entities. This simplifies managing relationships between entities in object-relational mapping.

## What is Cascade in Hibernate?

Cascading is the way to propagate the state change from a parent entity to a child entity. When you perform an operation like save, update, or delete on a parent entity, the same operation can be automatically applied to its associated entities if cascading is enabled.

## Cascade Types in Hibernate

Hibernate supports several cascade types that can be specified in the relationship annotations (`@OneToMany`, `@ManyToOne`, `@OneToOne`, `@ManyToMany`):

### 1. CascadeType.ALL

```java
@OneToMany(mappedBy = "parent", cascade = CascadeType.ALL)
private Set<Child> children = new HashSet<>();
```

- Applies all cascade operations (PERSIST, MERGE, REMOVE, REFRESH, DETACH).
- Use when the child entity's lifecycle is completely dependent on the parent.
- **Interview Answer**: "CascadeType.ALL propagates all operations from parent to child entities. It's useful when the child cannot exist without its parent, ensuring that any operation on the parent is mirrored to its children."

### 2. CascadeType.PERSIST

```java
@OneToMany(mappedBy = "parent", cascade = CascadeType.PERSIST)
private Set<Child> children = new HashSet<>();
```

- When a parent entity is saved, its associated child entities are also saved.
- **Interview Answer**: "CascadeType.PERSIST ensures that when you save a parent entity, any new child entities also get persisted. It's useful when you want to save an object graph in a single operation without having to explicitly save each child."

### 3. CascadeType.MERGE

```java
@OneToMany(mappedBy = "parent", cascade = CascadeType.MERGE)
private Set<Child> children = new HashSet<>();
```

- When a detached parent entity is reattached to the session, its associated child entities are also merged.
- **Interview Answer**: "CascadeType.MERGE is used when updating entities. If you merge a parent entity, its child entities will also be merged. This is particularly useful when working with detached entities that have been modified."

### 4. CascadeType.REMOVE

```java
@OneToMany(mappedBy = "parent", cascade = CascadeType.REMOVE)
private Set<Child> children = new HashSet<>();
```

- When a parent entity is deleted, its associated child entities are also deleted.
- **Interview Answer**: "CascadeType.REMOVE means that when you delete a parent entity, all its associated child entities are also deleted. This helps maintain referential integrity and prevent orphaned records, but should be used carefully to avoid unintended data loss."

### 5. CascadeType.DETACH

```java
@OneToMany(mappedBy = "parent", cascade = CascadeType.DETACH)
private Set<Child> children = new HashSet<>();
```

- When a parent entity is detached from the session, its associated child entities are also detached.
- **Interview Answer**: "CascadeType.DETACH propagates the detach operation. When a parent entity is detached from the session, all its child entities are also detached, meaning they're no longer managed by the persistence context."

### 6. CascadeType.REFRESH

```java
@OneToMany(mappedBy = "parent", cascade = CascadeType.REFRESH)
private Set<Child> children = new HashSet<>();
```

- When a parent entity is refreshed from the database, its associated child entities are also refreshed.
- **Interview Answer**: "CascadeType.REFRESH ensures that when you refresh a parent entity from the database, all its child entities are also refreshed. This is useful to ensure your entity graph reflects the most recent database state."

### 7. CascadeType.REPLICATE (Hibernate-specific)

```java
@OneToMany(mappedBy = "parent", cascade = org.hibernate.annotations.CascadeType.REPLICATE)
private Set<Child> children = new HashSet<>();
```

- Hibernate-specific cascade type, not part of JPA.
- Used during replication process.
- **Interview Answer**: "CascadeType.REPLICATE is a Hibernate-specific cascade type used during the replication process to copy entities from one database to another while preserving identities."

## Multiple Cascade Types

You can specify multiple cascade types for a relationship:

```java
@OneToMany(mappedBy = "parent", cascade = {CascadeType.PERSIST, CascadeType.MERGE})
private Set<Child> children = new HashSet<>();
```

- **Interview Answer**: "You can combine multiple cascade types to achieve the desired behavior. For example, cascading PERSIST and MERGE but not REMOVE ensures that saving and updating operations propagate to children, but deletion of the parent doesn't delete children."

## Orphan Removal

Often confused with cascading, but it's a separate concept:

```java
@OneToMany(mappedBy = "parent", orphanRemoval = true)
private Set<Child> children = new HashSet<>();
```

- When a child entity is removed from the collection, it's deleted from the database.
- **Interview Answer**: "orphanRemoval is different from cascade operations. It means that if a child entity is removed from the parent's collection (but not deleted explicitly), it will be deleted from the database. This ensures that no 'orphaned' child entities exist without a parent."

## Best Practices and Considerations

1. **Be Careful with Bidirectional Relationships**: In bidirectional relationships with CascadeType.ALL, you might encounter issues if both sides cascade. Consider cascading only from the owning side.

2. **Avoid CascadeType.REMOVE in Many-to-One**: Using CascadeType.REMOVE in a Many-to-One relationship can lead to unintended deletions of parent entities when a child is deleted.

3. **Consider the Domain Logic**: The cascade options should reflect your domain logic and entity relationships.

4. **Performance Implications**: Cascading operations can affect performance, especially with large object graphs. Use them judiciously.

## Common Interview Questions about Cascade in Hibernate

### 1. What is the difference between CascadeType.REMOVE and orphanRemoval=true?

**Answer**: "CascadeType.REMOVE propagates the delete operation from parent to child when the parent is explicitly deleted. orphanRemoval=true removes child entities when they're no longer referenced by a parent, like when they're removed from a collection. orphanRemoval is more about maintaining the relationship's integrity."

### 2. What cascade type should you use if you want to save a parent and its children in a single transaction?

**Answer**: "CascadeType.PERSIST would be appropriate here, as it ensures that when a parent entity is saved, any new associated child entities are also persisted to the database."

### 3. How do you prevent Hibernate from deleting child entities when a parent is deleted?

**Answer**: "You should avoid using CascadeType.REMOVE or CascadeType.ALL in your relationship definition. Instead, use specific cascade types like CascadeType.PERSIST and CascadeType.MERGE."

### 4. In a bidirectional relationship, should you define cascade on both sides?

**Answer**: "Generally, it's recommended to define cascade operations only on the owning side of the relationship to avoid potential issues with cascade loops. For most relationships, cascading from parent to child (one-to-many side) is the natural choice."

### 5. What happens if you use CascadeType.ALL with a large object graph?

**Answer**: "CascadeType.ALL will propagate all operations to the entire object graph, which could impact performance with large graphs. Operations like save or update might touch many more tables than necessary. It's important to be selective with cascade types in complex domain models."

### 6. How does Hibernate's cascading relate to database foreign key constraints?

**Answer**: "Hibernate's cascade operations are at the application level and don't directly affect database constraints. Database CASCADE constraints operate at the database level. Both should be aligned for consistency, but they work independently. Hibernate cascades affect how the ORM behaves, while database constraints ensure data integrity at the database level."


# Hibernate/JPA Relationships

Entity relationships are a fundamental concept in Hibernate and JPA. They define how objects are related to each other and how these relationships are mapped to the database schema.

## Relationship Annotations and Their Attributes

Before diving into specific relationship types, let's understand the common attributes that can be used with relationship annotations:

### Common Relationship Attributes

1. **cascade**: Specifies which operations should be cascaded to the associated entity
   ```java
   @OneToMany(cascade = {CascadeType.PERSIST, CascadeType.MERGE})
   ```

2. **fetch**: Controls when the related entities are loaded from the database
   ```java
   @ManyToOne(fetch = FetchType.LAZY)
   ```
   - `FetchType.EAGER`: Loads immediately with the owning entity (default for @ManyToOne and @OneToOne)
   - `FetchType.LAZY`: Loads only when explicitly accessed (default for @OneToMany and @ManyToMany)

3. **optional**: Determines whether the association is required or optional
   ```java
   @OneToOne(optional = false) // Makes the relationship mandatory
   ```

4. **orphanRemoval**: Controls whether orphaned entities should be removed when they're no longer referenced
   ```java
   @OneToMany(orphanRemoval = true)
   ```

5. **mappedBy**: Specifies the field that owns the relationship in bidirectional associations
   ```java
   @OneToMany(mappedBy = "department")
   ```

### @JoinColumn Attributes

1. **name**: Specifies the name of the foreign key column
   ```java
   @JoinColumn(name = "address_id")
   ```

2. **referencedColumnName**: The name of the column being referenced (defaults to primary key)
   ```java
   @JoinColumn(referencedColumnName = "id")
   ```

3. **nullable**: Whether the foreign key column is nullable
   ```java
   @JoinColumn(nullable = false)
   ```

4. **unique**: Whether the foreign key column has a unique constraint
   ```java
   @JoinColumn(unique = true)
   ```

5. **insertable/updatable**: Controls whether the column is included in SQL INSERT/UPDATE statements
   ```java
   @JoinColumn(insertable = true, updatable = false)
   ```

### @JoinTable Attributes

1. **name**: The name of the join table
   ```java
   @JoinTable(name = "student_course")
   ```

2. **joinColumns**: Specifies the foreign key column(s) for the owning entity
   ```java
   @JoinTable(joinColumns = @JoinColumn(name = "student_id"))
   ```

3. **inverseJoinColumns**: Specifies the foreign key column(s) for the non-owning entity
   ```java
   @JoinTable(inverseJoinColumns = @JoinColumn(name = "course_id"))
   ```

4. **uniqueConstraints**: Defines unique constraints on the join table
   ```java
   @JoinTable(uniqueConstraints = @UniqueConstraint(columnNames = {"student_id", "course_id"}))
   ```

## Types of Relationships in Hibernate/JPA

Hibernate supports four main types of relationships between entities:

### 1. @OneToOne Relationship

A one-to-one relationship exists when an entity is associated with exactly one instance of another entity.

```java
@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @OneToOne(cascade = CascadeType.ALL, fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "address_id", referencedColumnName = "id", nullable = false)
    private Address address;
    
    // Getters and setters
}

@Entity
public class Address {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String street;
    private String city;
    
    @OneToOne(mappedBy = "address", fetch = FetchType.LAZY)
    private Employee employee;
    
    // Getters and setters
}
```

**Important @OneToOne Specific Attributes**:
- `optional = false`: Makes the relationship mandatory (triggers NOT NULL constraint)
- `@JoinColumn(unique = true)`: Ensures the relationship is truly one-to-one at the database level

**Interview Answer**: "A @OneToOne relationship connects two entities in a 1:1 relationship. For instance, an Employee has one Address and an Address belongs to one Employee. The relationship can be unidirectional or bidirectional. In bidirectional relationships, the 'mappedBy' attribute designates the non-owning side. Setting optional=false ensures the relationship is mandatory, and a unique constraint on the join column enforces true one-to-one cardinality at the database level."

### 2. @OneToMany / @ManyToOne Relationship

A one-to-many relationship is where one entity instance is associated with multiple instances of another entity.

```java
@Entity
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @OneToMany(
        mappedBy = "department",
        cascade = CascadeType.ALL,
        orphanRemoval = true,
        fetch = FetchType.LAZY
    )
    @OrderBy("name ASC")  // Sort collection by name
    @BatchSize(size = 20) // Batch loading optimization
    private List<Employee> employees = new ArrayList<>();
    
    // Helper methods for bidirectional relationship maintenance
    public void addEmployee(Employee employee) {
        employees.add(employee);
        employee.setDepartment(this);
    }
    
    public void removeEmployee(Employee employee) {
        employees.remove(employee);
        employee.setDepartment(null);
    }
    
    // Getters and setters
}

@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "department_id", foreignKey = @ForeignKey(name = "FK_EMPLOYEE_DEPARTMENT"))
    private Department department;
    
    // Getters and setters
}
```

**Important @OneToMany Specific Attributes**:
- `orphanRemoval = true`: Removes child entities when they're removed from the collection
- `@OrderBy`: Specifies the ordering of the collection elements (SQL ORDER BY)
- `@OrderColumn`: Maintains a position/order column in the database
- `@BatchSize`: Configures batch loading to optimize collection fetching
- `@Where`: Filters the collection using a SQL WHERE clause
- `@Filter`: Allows for dynamic filtering of collection elements

**Important @ManyToOne Specific Attributes**:
- `foreignKey = @ForeignKey(name = "...")`: Names the foreign key constraint
- `optional = false`: Specifies that the association is required (NOT NULL constraint)

**Interview Answer**: "A @OneToMany relationship allows an entity to have a collection of other entities. For example, a Department can have many Employees. It's typically paired with @ManyToOne on the other side for a bidirectional relationship. The @ManyToOne side is usually the owning side and contains the foreign key. To maintain relationship integrity, it's good practice to implement helper methods in the parent entity that manage both sides of the relationship. Additional attributes like @OrderBy, @BatchSize and @Filter can optimize collection handling and querying. The @ManyToOne side can specify foreign key constraints and whether the association is mandatory."

### 3. @ManyToMany Relationship

A many-to-many relationship exists when multiple instances of an entity can be associated with multiple instances of another entity.

```java
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @ManyToMany(
        cascade = {CascadeType.PERSIST, CascadeType.MERGE},
        fetch = FetchType.LAZY
    )
    @JoinTable(
        name = "student_course", 
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id"),
        uniqueConstraints = @UniqueConstraint(
            columnNames = {"student_id", "course_id"}
        ),
        foreignKey = @ForeignKey(name = "FK_STUDENT_COURSE"),
        inverseForeignKey = @ForeignKey(name = "FK_COURSE_STUDENT")
    )
    @BatchSize(size = 30)
    @OrderBy("name ASC")
    private Set<Course> courses = new HashSet<>();
    
    // Helper methods for relationship management
    public void addCourse(Course course) {
        this.courses.add(course);
        course.getStudents().add(this);
    }
    
    public void removeCourse(Course course) {
        this.courses.remove(course);
        course.getStudents().remove(this);
    }
    
    // Getters and setters
}

@Entity
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @ManyToMany(
        mappedBy = "courses",
        fetch = FetchType.LAZY
    )
    private Set<Student> students = new HashSet<>();
    
    // Getters and setters
}
```

**Important @ManyToMany Specific Attributes**:
- `@JoinTable`: Defines the join table structure:
  - `name`: Name of the join table
  - `joinColumns`: Columns referring to the owning side entity
  - `inverseJoinColumns`: Columns referring to the non-owning side entity
  - `uniqueConstraints`: Ensures no duplicate relationships
  - `foreignKey`/`inverseForeignKey`: Names and customizes the foreign key constraints

- `@MapKeyColumn`: For Map collections, specifies the map key column
- `@MapKeyJoinColumn`: For entity-based map keys
- `@MapKeyEnumerated`/`@MapKeyTemporal`: For enum or temporal map keys

**Interview Answer**: "A @ManyToMany relationship connects entities where each can be related to multiple instances of the other. For example, Students can enroll in multiple Courses, and each Course can have multiple Students. It requires a join table in the database that contains foreign keys to both entities. The @JoinTable annotation configures this table, including its name, foreign key columns, and constraints. For optimal performance, it's important to use LAZY fetching and consider using helper methods to maintain both sides of the relationship consistently. Hibernate provides additional annotations like @MapKeyColumn for mapping Maps and @OrderBy for controlling collection ordering."

### 4. Self-Referencing Relationship

An entity can have a relationship with itself (recursive relationship).

```java
@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "manager_id", foreignKey = @ForeignKey(name = "FK_EMP_MANAGER"))
    private Employee manager;
    
    @OneToMany(mappedBy = "manager", cascade = CascadeType.ALL, orphanRemoval = true)
    @OrderBy("name ASC")
    private List<Employee> subordinates = new ArrayList<>();
    
    // Helper methods for maintaining relationship integrity
    public void addSubordinate(Employee employee) {
        subordinates.add(employee);
        employee.setManager(this);
    }
    
    public void removeSubordinate(Employee employee) {
        subordinates.remove(employee);
        employee.setManager(null);
    }
    
    // Getters and setters
}
```

**Interview Answer**: "Self-referencing relationships allow entities to relate to instances of the same entity type. A common example is an Employee-Manager relationship, where each Employee can have one Manager and a Manager can have multiple subordinate Employees. These relationships use the same annotations as regular relationships but reference the same entity class. It's important to be careful with cascading operations in self-referencing relationships to avoid infinite loops or unexpected deletions."

## Additional Relationship Mapping Strategies and Attributes

### Embeddable Objects and @Embedded

Embedding value objects directly into an entity:

```java
@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "line1", column = @Column(name = "home_address_line1")),
        @AttributeOverride(name = "city", column = @Column(name = "home_address_city"))
    })
    private Address homeAddress;
    
    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "line1", column = @Column(name = "work_address_line1")),
        @AttributeOverride(name = "city", column = @Column(name = "work_address_city"))
    })
    private Address workAddress;
    
    // Getters and setters
}

@Embeddable
public class Address {
    private String line1;
    private String city;
    private String zipCode;
    
    // Getters and setters
}
```

**Interview Answer**: "The @Embeddable and @Embedded annotations allow you to reuse a class across multiple entities without creating separate tables. The embedded object's fields are mapped directly to columns in the containing entity's table. @AttributeOverrides let you customize column names when embedding the same type multiple times."

### Element Collections

Mapping collections of basic or embeddable types:

```java
@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @ElementCollection
    @CollectionTable(
        name = "employee_phone_numbers",
        joinColumns = @JoinColumn(name = "employee_id")
    )
    @Column(name = "phone_number")
    private Set<String> phoneNumbers = new HashSet<>();
    
    @ElementCollection
    @CollectionTable(
        name = "employee_addresses",
        joinColumns = @JoinColumn(name = "employee_id")
    )
    private List<Address> addresses = new ArrayList<>();
    
    // Getters and setters
}
```

**Interview Answer**: "@ElementCollection allows you to map collections of basic types (String, Integer) or embeddable objects without needing to create full entity classes for them. It creates a separate collection table with a foreign key to the owning entity. This is ideal for simple value collections that don't need their own identity."

### @Any and @ManyToAny for Polymorphic Associations

Used for mapping relationships to multiple entity types:

```java
@Entity
public class Comment {
    @Id
    @GeneratedValue
    private Long id;
    
    private String text;
    
    @Any
    @JoinColumn(name = "content_id")
    @JoinTable(
        name = "comment_content",
        joinColumns = @JoinColumn(name = "comment_id"),
        inverseJoinColumns = @JoinColumn(name = "content_id")
    )
    @AnyMetaDef(
        idType = "long",
        metaType = "string",
        metaValues = {
            @MetaValue(targetEntity = Post.class, value = "P"),
            @MetaValue(targetEntity = Image.class, value = "I"),
            @MetaValue(targetEntity = Video.class, value = "V")
        }
    )
    @Column(name = "content_type")
    private Commentable content;
    
    // Getters and setters
}
```

**Interview Answer**: "@Any and @ManyToAny annotations enable polymorphic associations where a field can reference entities of different types. This is useful when you need to implement a feature like comments that can be attached to various entity types (posts, images, videos). It's a Hibernate-specific feature that provides more flexibility than standard JPA relationships."

### Ordering and Sorting Collections

```java
// Database-level ordering
@OneToMany(mappedBy = "post")
@OrderBy("creationDate DESC")
private List<Comment> comments = new ArrayList<>();

// Application-level sorting
@OneToMany(mappedBy = "post")
@org.hibernate.annotations.Sort(
    type = org.hibernate.annotations.SortType.NATURAL
)
private SortedSet<Comment> sortedComments = new TreeSet<>();
```

**Interview Answer**: "Hibernate offers multiple ways to order collections. @OrderBy performs database-level sorting using an SQL ORDER BY clause, while @Sort handles in-memory sorting with Java comparators. @OrderColumn maintains an explicit position column in the database table that stores the collection elements' order."

### Native SQL Customization

```java
@Entity
@Table(name = "employees")
@SQLInsert(sql = "INSERT INTO employees (id, name, version, created_at) VALUES (?, ?, ?, NOW())")
@SQLUpdate(sql = "UPDATE employees SET name = ?, version = ? WHERE id = ? AND version = ?")
public class Employee {
    // Entity fields
}
```

**Interview Answer**: "Hibernate allows customizing the SQL statements used for CRUD operations with annotations like @SQLInsert, @SQLUpdate, and @SQLDelete. This is useful for implementing advanced database features like auditing, soft deletes, or optimistic locking that require custom SQL logic."

### Formula and Derived Properties

```java
@Entity
public class Product {
    @Id
    private Long id;
    
    private BigDecimal price;
    private BigDecimal taxRate;
    
    @Formula("price * tax_rate")
    private BigDecimal priceWithTax;
    
    // Getters and setters
}
```

**Interview Answer**: "The @Formula annotation allows you to define a calculated property using a SQL expression. The database calculates this value when the entity is loaded, and it's read-only in the entity. This is useful for derived properties that depend on multiple columns or require complex calculations."

## Advanced Relationship Features

### Inheritance Mapping Strategies

Hibernate provides several strategies for mapping inheritance hierarchies:

#### 1. Single Table Strategy (@Inheritance)

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "payment_type", discriminatorType = DiscriminatorType.STRING)
public abstract class Payment {
    @Id
    @GeneratedValue
    private Long id;
    private BigDecimal amount;
    // Common fields and methods
}

@Entity
@DiscriminatorValue("CC")
public class CreditCardPayment extends Payment {
    private String cardNumber;
    private String expiryDate;
    // Credit card specific fields and methods
}

@Entity
@DiscriminatorValue("BA")
public class BankTransferPayment extends Payment {
    private String bankName;
    private String accountNumber;
    // Bank transfer specific fields and methods
}
```

**Interview Answer**: "Single table inheritance maps an entire class hierarchy to a single database table. It uses a discriminator column to identify which subclass each row represents. This approach offers the best performance but can lead to many nullable columns if subclasses have many specific fields."

#### 2. Joined Table Strategy

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
public abstract class Payment {
    @Id
    @GeneratedValue
    private Long id;
    private BigDecimal amount;
    // Common fields and methods
}

@Entity
@PrimaryKeyJoinColumn(name = "payment_id")
public class CreditCardPayment extends Payment {
    private String cardNumber;
    private String expiryDate;
    // Credit card specific fields
}

@Entity
@PrimaryKeyJoinColumn(name = "payment_id")
public class BankTransferPayment extends Payment {
    private String bankName;
    private String accountNumber;
    // Bank transfer specific fields
}
```

**Interview Answer**: "Joined table inheritance uses a separate table for each class in the hierarchy, with foreign key relationships between parent and child tables. This normalizes the data structure but requires joins when querying subclasses, which can impact performance for deep hierarchies."

#### 3. Table Per Class Strategy

```java
@Entity
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
public abstract class Payment {
    @Id
    @GeneratedValue
    private Long id;
    private BigDecimal amount;
    // Common fields and methods
}

@Entity
public class CreditCardPayment extends Payment {
    private String cardNumber;
    private String expiryDate;
    // Credit card specific fields
}

@Entity
public class BankTransferPayment extends Payment {
    private String bankName;
    private String accountNumber;
    // Bank transfer specific fields
}
```

**Interview Answer**: "Table per class strategy creates a separate table for each concrete class in the hierarchy, with all fields (including those inherited from parent classes). This approach avoids joins but makes polymorphic queries inefficient as they need to use UNION operations across tables."

### Native Hibernate Collection Types

Hibernate supports specialized collection mappings beyond standard JPA:

```java
@Entity
public class User {
    @Id
    @GeneratedValue
    private Long id;
    
    // Sorted collections
    @OneToMany(mappedBy = "user")
    @SortNatural
    private SortedSet<Post> postsBySortOrder = new TreeSet<>();
    
    // Bags (allow duplicates, no order)
    @ElementCollection
    @CollectionType(type = "org.hibernate.collection.internal.PersistentBag")
    private Collection<String> tags = new ArrayList<>();
    
    // Maps with entity keys
    @ManyToMany
    @MapKeyJoinColumn(name = "role_id")
    private Map<Role, Permission> rolePermissions = new HashMap<>();
    
    // Lists with index column
    @OneToMany
    @OrderColumn(name = "position")
    private List<Task> orderedTasks = new ArrayList<>();
}
```

**Interview Answer**: "Hibernate supports specialized collection mappings beyond standard JPA including sorted collections with @SortNatural/@SortComparator, bags (collections that allow duplicates without order), indexed lists with @OrderColumn, and various map implementations with different key types (@MapKeyJoinColumn, @MapKeyColumn, etc.)."

## Common Interview Questions About Relationships in Hibernate

### 1. What's the difference between a unidirectional and bidirectional relationship?

**Answer**: "A unidirectional relationship allows navigation in only one direction, while a bidirectional relationship allows navigation in both directions. For example, in a unidirectional One-to-Many, a Parent can access its Children, but Children can't access their Parent. In a bidirectional relationship, both can access each other. Bidirectional relationships offer better navigability but require consistent management on both sides to maintain data integrity."

### 2. How do you choose between FetchType.EAGER and FetchType.LAZY?

**Answer**: "You should generally prefer LAZY loading to avoid performance issues, especially for collections. EAGER loading can lead to the N+1 query problem or excessive data loading. However, EAGER might be appropriate for associations that are always needed with the main entity and are relatively small and stable. The default fetch strategies are EAGER for @ManyToOne and @OneToOne, and LAZY for @OneToMany and @ManyToMany. It's important to override these defaults based on your application's access patterns."

### 3. What is the N+1 query problem and how can you solve it?

**Answer**: "The N+1 query problem occurs when you fetch N entities and then access a lazily-loaded collection for each, resulting in N additional queries. Solutions include:
1. Using join fetch in JPQL queries: `SELECT d FROM Department d JOIN FETCH d.employees`
2. Using EntityGraph for specific use cases: `@EntityGraph(attributePaths = {"employees"})`
3. Implementing batch fetching with `@BatchSize` annotation
4. Using the `@Fetch(FetchMode.SUBSELECT)` annotation
5. Creating DTO projections that load exactly what's needed in a single query
6. Leveraging second-level caching for frequently accessed entities"

### 4. How would you implement a many-to-many relationship with additional attributes on the relationship?

**Answer**: "I would create an explicit entity for the join table instead of using @ManyToMany. For example, with Students and Courses, I'd create a StudentCourse entity with additional fields like enrollmentDate or grade, and then use two @OneToMany/@ManyToOne relationships."

```java
@Entity
public class StudentCourse {
    @Id
    @GeneratedValue
    private Long id;
    
    @ManyToOne
    @JoinColumn(name = "student_id", nullable = false)
    private Student student;
    
    @ManyToOne
    @JoinColumn(name = "course_id", nullable = false)
    private Course course;
    
    private Date enrollmentDate;
    private String grade;
    
    // Constructor for easy relationship creation
    public StudentCourse(Student student, Course course) {
        this.student = student;
        this.course = course;
        this.enrollmentDate = new Date();
    }
    
    // Appropriate equals and hashCode methods
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof StudentCourse)) return false;
        StudentCourse that = (StudentCourse) o;
        return Objects.equals(student.getId(), that.student.getId()) &&
               Objects.equals(course.getId(), that.course.getId());
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(student.getId(), course.getId());
    }
    
    // Getters and setters
}
```

### 5. What are the implications of cascade operations in relationships?

**Answer**: "Cascading operations can simplify entity management but must be used carefully. For example, CascadeType.REMOVE on a @OneToMany relationship means deleting a parent will delete all children. This might be desirable for strong ownership relationships but dangerous for others. It's generally safer to specify exactly which operations should cascade rather than using CascadeType.ALL. Performance implications are also important to consider, especially with large object graphs. In bidirectional relationships, cascading from both sides can lead to infinite loops during operations like persist or remove."

### 6. How can you optimize the performance of entity relationships?

**Answer**: "Several strategies can optimize relationship performance:
1. Use appropriate fetch types (usually LAZY) based on access patterns
2. Implement strategic use of join fetches with JPQL or Criteria API
3. Use EntityGraph for specific use cases that need related entities
4. Configure batch fetching with @BatchSize to reduce the number of queries
5. Implement pagination for large collections using setFirstResult/setMaxResults
6. Use database-level ordering with @OrderBy instead of in-memory sorting
7. Consider second-level caching for frequently accessed entities and collections
8. Use read-only transactions for queries that don't modify data
9. Create specific DTO projections for read operations to avoid loading unnecessary data
10. Optimize equals/hashCode implementation for entities in collections"

### 7. What's the difference between @JoinColumn and @JoinTable?

**Answer**: "The @JoinColumn annotation is used in @OneToOne and @ManyToOne relationships to specify the foreign key column directly in the owning entity's table. It allows customization of the foreign key column name, constraints, and other properties. @JoinTable is used primarily in @ManyToMany relationships to define an intermediate join table that contains foreign keys to both entities in the relationship. It allows specifying the join table name, its columns, and constraints. @JoinTable can also be used with @OneToMany relationships to implement a unidirectional one-to-many relationship without requiring a mappedBy attribute."

### 8. How do you handle bidirectional relationship consistency?

**Answer**: "To maintain bidirectional relationship consistency, it's essential to update both sides of the relationship in synchronized helper methods. For example, in a Parent-Child relationship:

```java
public void addChild(Child child) {
    this.children.add(child);
    child.setParent(this);
}

public void removeChild(Child child) {
    this.children.remove(child);
    child.setParent(null);
}
```

These methods ensure that both sides stay consistent. Without them, you risk orphaned records or inconsistent state when objects are detached and later merged back to the persistence context."

### 9. How do you choose the right collection type for entity relationships?

**Answer**: "The choice depends on your requirements:
- Use `List` when order is important or duplicates are allowed
- Use `Set` when you need uniqueness and order isn't important
- Use `Map` when you need key-based access to related entities
- Use `SortedSet` or `SortedMap` when you need sorted collections

Each has performance implications. Sets and Maps use hashCode/equals for lookups, while Lists may require full traversal. For large collections, Sets typically offer better performance for contains/remove operations. Always implement equals/hashCode correctly for entities stored in Set or Map collections."