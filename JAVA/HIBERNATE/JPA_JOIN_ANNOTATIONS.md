# Understanding JPA Join Annotations

## @JoinColumn - Foreign Key Management

The `@JoinColumn` annotation defines the foreign key column that links entities in a relationship.

### Essential Attributes

```java
@ManyToOne
@JoinColumn(
    name = "department_id",           // Foreign key column name
    referencedColumnName = "id",      // Referenced column (defaults to PK)
    nullable = false,                 // Creates NOT NULL constraint
    unique = true,                    // Creates UNIQUE constraint
    foreignKey = @ForeignKey(name = "FK_EMPLOYEE_DEPT") // Names the FK constraint
)
private Department department;
```

#### Core Attributes Explained

1. **name**: 
   - Specifies the name of the foreign key column in the database
   - Default naming convention: `referenced_entity_name` + `_` + `referenced_column_name`
   - Example: `department_id`

2. **referencedColumnName**:
   - Specifies which column in the target entity is being referenced
   - Defaults to the primary key column of the target entity
   - Example: `@JoinColumn(referencedColumnName = "uuid")` to reference a non-PK column

3. **nullable**:
   - Controls whether the foreign key column allows NULL values
   - Creates a `NOT NULL` constraint at the database level when set to `false`
   - Example: `@JoinColumn(nullable = false)` for required relationships

4. **unique**:
   - Creates a `UNIQUE` constraint on the foreign key column
   - Essential for true one-to-one relationships
   - Example: `@JoinColumn(unique = true)` ensures no duplicate references

5. **foreignKey**:
   - Customizes the foreign key constraint created in the database
   - Example: `@ForeignKey(name = "FK_EMPLOYEE_DEPT")`

### Key Use Cases

#### 1. @OneToOne Relationships

```java
// Owning side - has the foreign key
@Entity
public class Employee {
    @OneToOne
    @JoinColumn(name = "address_id", unique = true)
    private Address address;
}

// Non-owning side - no column in database
@Entity
public class Address {
    @OneToOne(mappedBy = "address")
    private Employee employee;
}
```

The `unique = true` attribute is crucial here as it enforces true one-to-one cardinality at the database level.

#### 2. @ManyToOne Required Relationships

```java
@ManyToOne(optional = false)
@JoinColumn(name = "department_id", nullable = false)
private Department department;
```

Using both `optional = false` and `nullable = false` enforces the constraint at both JPA and database levels.

#### 3. @OneToMany with Direct Foreign Key (no join table)

```java
// Unidirectional one-to-many without join table
@OneToMany
@JoinColumn(name = "parent_id") // FK in child table
private List<Child> children;
```

This creates a foreign key column in the Child table without requiring a join table.

#### 4. Composite Foreign Keys

```java
@ManyToOne
@JoinColumns({
    @JoinColumn(name = "emp_id", referencedColumnName = "id"),
    @JoinColumn(name = "emp_dept", referencedColumnName = "dept_code")
})
private Employee employee;
```

Use `@JoinColumns` (plural) to create a composite foreign key relationship.

#### 5. Read-Only Relationships

```java
@ManyToOne
@JoinColumn(name = "dept_id", insertable = false, updatable = false)
private Department readOnlyDepartment;
```

The `insertable=false, updatable=false` attributes make this a read-only relationship, useful when mapping the same foreign key column twice.

## @JoinTable - Many-to-Many Relationships

The `@JoinTable` annotation configures the join table used to link entities in many-to-many relationships.

### Essential Attributes

```java
@ManyToMany
@JoinTable(
    name = "student_course",                       // Join table name
    joinColumns = @JoinColumn(name = "student_id"), // FK to this entity
    inverseJoinColumns = @JoinColumn(name = "course_id"), // FK to other entity
    uniqueConstraints = @UniqueConstraint(
        columnNames = {"student_id", "course_id"}  // Prevents duplicates
    )
)
private Set<Course> courses;
```

#### Core Attributes Explained

1. **name**: 
   - Specifies the name of the join table in the database
   - Default: `owning_entity` + `_` + `non_owning_entity`
   - Example: `student_course`

2. **joinColumns**:
   - Defines the foreign key column(s) in the join table that reference the owning entity
   - Example: `@JoinColumn(name = "student_id")`

3. **inverseJoinColumns**:
   - Defines the foreign key column(s) in the join table that reference the non-owning entity
   - Example: `@JoinColumn(name = "course_id")`

4. **uniqueConstraints**:
   - Creates unique constraints on the join table
   - Prevents duplicate relationships
   - Example: `@UniqueConstraint(columnNames = {"student_id", "course_id"})`

### Key Use Cases

#### 1. Standard Many-to-Many

```java
@Entity
public class Student {
    @ManyToMany
    @JoinTable(
        name = "student_course", 
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private Set<Course> courses;
}

@Entity
public class Course {
    @ManyToMany(mappedBy = "courses")
    private Set<Student> students;
}
```

#### 2. Many-to-Many with Additional Constraints

```java
@ManyToMany
@JoinTable(
    name = "employee_project",
    joinColumns = @JoinColumn(name = "employee_id"),
    inverseJoinColumns = @JoinColumn(name = "project_id"),
    uniqueConstraints = @UniqueConstraint(
        columnNames = {"employee_id", "project_id"}
    ),
    foreignKey = @ForeignKey(name = "FK_EMP_PROJ"),
    inverseForeignKey = @ForeignKey(name = "FK_PROJ_EMP")
)
private Set<Project> projects;
```

#### 3. Self-Referencing Many-to-Many (e.g., Friends relationship)

```java
@Entity
public class Person {
    @ManyToMany
    @JoinTable(
        name = "friendship",
        joinColumns = @JoinColumn(name = "person_id"),
        inverseJoinColumns = @JoinColumn(name = "friend_id")
    )
    private Set<Person> friends;
}
```

## Important Distinctions

### 1. @JoinColumn vs mappedBy

- **@JoinColumn**:
  - Used on the owning side of the relationship
  - Defines an actual database column
  - Controls the foreign key characteristics

- **mappedBy**:
  - Used on the non-owning side of bidirectional relationships
  - Refers to the field name on the owning side
  - No additional database column is created

### 2. @JoinColumn vs @PrimaryKeyJoinColumn

- **@JoinColumn**:
  - Creates a separate foreign key column
  - Standard approach for most relationships

- **@PrimaryKeyJoinColumn**:
  - Primary key is also a foreign key 
  - Used in:
    - One-to-one with shared primary key
    - Joined table inheritance
  - Example: 
    ```java
    @Entity
    public class EmployeeDetails {
        @Id
        private Long id;
        
        @OneToOne
        @PrimaryKeyJoinColumn
        private Employee employee; // References Employee with same ID
    }
    ```

## Interview Q&A

**Q: What happens if you don't specify @JoinColumn in a @ManyToOne relationship?**  
A: "JPA uses default naming: referenced entity name + '_' + referenced PK column. For example, a Department entity with PK 'id' would create a 'department_id' foreign key."

**Q: What's the difference between @JoinColumn(nullable=false) and @ManyToOne(optional=false)?**  
A: "@JoinColumn(nullable=false) creates a database-level NOT NULL constraint. @ManyToOne(optional=false) enforces validation at the JPA level. Best practice is to use both together for required relationships."

**Q: How do you ensure a proper bidirectional @OneToOne relationship?**  
A: "On the owning side, use @JoinColumn with unique=true to create a unique constraint. On the non-owning side, use mappedBy to reference the owning side's field. This ensures true one-to-one cardinality at the database level."

**Q: When would you use @JoinColumns (plural) instead of @JoinColumn?**  
A: "Use @JoinColumns when implementing a relationship with a composite foreign key - where multiple columns together form the foreign key reference. This is common when the referenced entity has a composite primary key or when you need to reference non-primary key columns."

**Q: In a @ManyToMany relationship, what happens if you don't specify @JoinTable?**  
A: "JPA will create a default join table with a generated name following the pattern: owning_entity_name + '_' + referenced_entity_collection_name. The foreign key columns will also use default naming conventions. While this works, explicitly defining @JoinTable is better for readability and control."
