# Data Mining and Knowledge Extraction - Lecture 2
**Course Code:** 0509495  
**Professor:** Prof. Marco Calautti  
**Topic:** Database Design - Entity-Relationship Model  
**Duration:** ~2 hours 33 minutes

---

## Introduction to Database Design

### What is Database Design?
Database design is essentially the very first phase you encounter when you need to realize your database. Remember, we are going to focus on relational databases, which means we use the relational data model - roughly speaking, we try to organize our data in relations or, in more practical terms, tables.

Before we can go directly to the database system (whatever real-world database management system we are going to use), we need to first understand the data that we want to store and how to organize it.

### Technology Independence
Usually we try to do database design in a way that is independent of the actual data model, because:
- Sometimes you might need to store your data with a graph data model
- Sometimes you need to store data with a relational data model

To avoid being locked into a specific data model, we use a more generic design language - mostly a **graphical language** that has the main purpose of forcing you to properly understand:
1. **What are the entities** of the data you want to store (e.g., students, customers)
2. **What are the relationships** between these entities

This is how we usually try to see our data whenever we want it structured. It's important to identify these two components: the entities and the relationships that exist between the entities.

---

## The Entity-Relationship (ER) Model

### Overview
Today we're going to start showing you how we do this with an approach called the **Entity-Relationship Model**. This is **not** a data model in the sense of a database data model. It's more of a graphical language we agreed to use with some conventions and rules that show in a concise and immediate way how we believe the database should be designed and constructed.

The title says "entity relationship," which means there are two key elements in this graphical language:
1. **Entities**
2. **Relationships**

---

## Entities and Entity Sets

### What is an Entity?
An **entity** is a single object of the real world that is somehow distinguishable from others. For example:
- An employee is distinguished from a customer or a manager
- Each entity is described by its own **attributes**

### What is an Entity Set?
An **entity set** is a collection of similar entities - entities that all share the same attributes.

### ER Diagram Representation
In ER diagrams, we represent entity sets as:
- **Rectangle/Box**: Contains the name of the entity set (plural form, e.g., "Employees")
- **Ovals attached to the box**: Represent the attributes of the entity set

**Example: Employee Entity Set**
```
┌─────────────┐
│  Employees  │
└─────────────┘
      │
   ┌──┴──┬──────┬─────────┐
   │     │      │         │
  SSN   Name   Job   Parking_Lot
```

### Key Concepts for Entity Sets

#### 1. Domain of Attributes
- Each attribute must have a **data type** (domain)
- Examples: Name (string), Parking_Lot (number), SSN (string)
- Sometimes not written in diagram for clarity, but should be known

#### 2. Key Attributes
- **Key**: An attribute (or combination of attributes) that uniquely identifies entities
- Represented by **underlining** the attribute name in the diagram
- **Constraint**: Cannot have two different entities with the same key value
- Example: SSN is the key for employees - cannot have two employees with the same SSN

### Purpose of ER Diagrams
ER diagrams describe the **schema** of the database - the structure and constraints, not the actual data. We never talk about concrete entities (like "Marco with SSN 123456"), but rather describe the types of entities the database should be able to store.

---

## Relationships and Relationship Sets

### What is a Relationship?
A **relationship** tells you concretely if a specific entity and another specific entity are connected together or not.

**Example**: In a movie database
- Single relationship: "Arnold plays in the movie Terminator"
- This connects the specific actor "Arnold" with the specific movie "Terminator"

### What is a Relationship Set?
A **relationship set** is a collection of relationships of the same kind. We describe these with:
- **Diamond shape**: Contains the name of the relationship
- **Lines**: Connect the diamond to participating entity sets

**Example: Actors and Movies**
```
┌─────────┐    plays_in    ┌─────────┐
│ Actors  │◊────────────◊│ Movies  │
└─────────┘              └─────────┘
```

### Basic Relationship Properties
With the basic relationship set representation (diamond with simple lines):
- **Maximum flexibility**: Can store relationships in any possible way
- **Multiple relationships allowed**: Same actor can appear in many movies, same movie can have many actors
- **Optional participation**: Entities don't have to participate in relationships

---

## Types of Relationship Sets

### 1. Many-to-Many Relationships
- **Most flexible** type of relationship
- Represented with **simple lines** on both sides
- Example: Actors and Movies
  - Many actors can play in the same movie
  - Many movies can feature the same actor

**Visualization as table**:
```
Actor         | Movie
--------------|-------------
Sharon Stone  | Basic Instinct
Sharon Stone  | Total Recall
Arnold        | Total Recall
Arnold        | Terminator
```

### 2. Many-to-One Relationships
- **Constraint**: Many entities on one side can connect to only one entity on the other side
- Represented with an **arrow** pointing to the "one" side
- Example: Employees and Departments
  - Many employees can work in the same department
  - Each employee works in at most one department

**How to read**: Go to the side with the arrow - each entity there can connect to **at most one** entity on the other side.

```
┌───────────┐     works_in     ┌─────────────┐
│ Employees │────────────────→│ Departments │
└───────────┘                 └─────────────┘
```

### 3. One-to-One Relationships
- **Strongest constraint**: Each entity on both sides connects to at most one entity on the other side
- Represented with **arrows on both sides**
- Example: Managers and Departments
  - Each manager runs at most one department
  - Each department has at most one manager

```
┌──────────┐     manages     ┌─────────────┐
│ Managers │←──────────────→│ Departments │
└──────────┘                └─────────────┘
```

### 4. Total Participation
- **Additional constraint**: Forces entities to participate in relationships
- Represented with **thick lines**
- Meaning: Each entity **must** be connected to at least one entity on the other side

**Example**: Movies must have at least one actor
```
┌─────────┐    stars_in    ┌─────────┐
│ Movies  │■────────────◊│ Actors  │
└─────────┘              └─────────┘
```

### Combining Constraints
You can combine arrows and thick lines:
- **Arrow + Thick line** = Exactly one (must participate, and connects to exactly one)
- Example: Each employee has exactly one department

**Reading Guidelines**:
1. **Simple line**: Zero or more connections allowed
2. **Arrow**: At most one connection
3. **Thick line**: At least one connection (must participate)
4. **Arrow + Thick line**: Exactly one connection

---

## Practical Exercise: Music Recording Studio Database

### Initial Requirements
Design a database for a music recording company that wants to store information about:

#### Musicians
- Social Security Number (SSN)
- Name  
- Address
- Telephone number

**Initial Entity Set**:
```
┌──────────┐
│Musicians │
└──────────┘
     │
 ┌───┼───┬─────────┬───────────┐
 │   │   │         │           │
SSN Name Address Telephone
```

### Addressing Data Consistency Issues

#### Problem Identified
The requirements specify:
- Musicians often share addresses (can't afford individual apartments)
- Multiple musicians may share the same phone number
- Each address has only one phone number

#### Issue with Simple Approach
If we store address and phone as attributes of musicians:
- Marco: Address "Via Roma", Phone "777"
- Andrea: Address "Via Roma", Phone "111" ← **Inconsistency!**

The same address has different phone numbers, violating the constraint.

#### Solution: Separate Entity Sets
**Corrected Design**:

1. **Musicians Entity Set**:
   - SSN (key)
   - Name

2. **Locations Entity Set**:
   - Address (key)

3. **Phone Numbers Entity Set**:
   - Number (key)

**Relationships**:
- Musicians → Locations: Many-to-one with total participation (each musician lives in exactly one location)
- Locations → Phone Numbers: One-to-one with total participation (each location has exactly one phone number)

```
┌──────────┐   lives_in   ┌───────────┐   has_phone   ┌──────────────┐
│Musicians │■────────────→│ Locations │■─────────────■│Phone Numbers │
└──────────┘              └───────────┘              └──────────────┘
```

This design enforces:
- Multiple musicians can share the same address
- Each address has exactly one phone number
- No inconsistencies possible

### Adding Musical Instruments

#### Requirements
- Musicians can borrow instruments to play
- Each instrument has a unique identifying number
- Store instrument name and musical key
- Track which musicians play which instruments

#### Design
**Instruments Entity Set**:
- ID (key)
- Name  
- Musical_Key

**Relationship**: Musicians ↔ Instruments (Many-to-Many)
- A musician can play zero or more instruments
- An instrument can be played by zero or more musicians

```
┌──────────┐     plays     ┌─────────────┐
│Musicians │◊────────────◊│ Instruments │
└──────────┘              └─────────────┘
```

### Adding Albums and Songs

#### Albums Entity Set
- Album_ID (key)
- Title
- Copyright_Date
- Format

#### Songs Entity Set  
- Song_ID (key)
- Title
- Author (as simple string attribute - external person, not necessarily a musician)

#### Relationships
1. **Albums ↔ Songs**: One-to-Many
   - Each album can have many songs
   - Each song appears in at most one album

2. **Musicians ↔ Songs**: Many-to-Many (Performance)
   - Musicians can perform multiple songs
   - Songs can be performed by multiple musicians
   - May require total participation: each song must be performed by at least one musician

3. **Musicians ↔ Albums**: Many-to-One (Production)
   - Each album must have exactly one producer (musician who pays for recording)
   - A musician can produce zero or more albums

### Handling Borrowing History

#### Problem with Basic Relationship
Basic "plays" relationship between Musicians and Instruments doesn't track borrowing history:
- Can only store that "Marco plays Instrument 3"
- Cannot store multiple borrowing events with dates

#### Solution: Elevate to Entity Set
Create **Borrows Entity Set**:
- Date (or add artificial ID as key)
- Related to exactly one musician
- Related to exactly one instrument (or many if tracking "receipts")

```
┌──────────┐   performs   ┌─────────┐   involves   ┌─────────────┐
│Musicians │■────────────→│ Borrows │■────────────→│ Instruments │
└──────────┘              └─────────┘              └─────────────┘
```

This allows tracking:
- Multiple borrowing events for the same musician-instrument pair
- Dates for each borrowing event
- Complete borrowing history

---

## Advanced ER Model Features

### Multi-Way Relationships

#### When Needed
Sometimes you need to relate three or more entities together in a single relationship, not as separate binary relationships.

#### Example Problem: Movie Contracts
**Incorrect approach** with binary relationships:
- Actors ↔ Movies (who stars in what)
- Actors ↔ Studios (who has contracts with whom)

**Problem**: Cannot determine which studio paid which actor for which specific movie.

#### Solution: Ternary Relationship
Connect three entity sets with one relationship:
- **Contract** relationship connecting Actors, Movies, and Studios
- Stores triples like: (Arnold, Terminator, Paramount)

```
        ┌─────────┐
        │ Actors  │
        └─────────┘
             │
             │
    ┌────────◊────────┐
    │                 │
┌─────────┐      ┌─────────┐
│ Movies  │      │ Studios │
└─────────┘      └─────────┘
```

#### Reading Multi-Way Relationships
For each entity set, ask: "If I pick one entity from this side, how many combinations of the other entities can it connect to?"

Example: If I pick actor "Sharon Stone":
- Can connect to pairs like: (Basic Instinct, Sony), (Total Recall, Columbia), (Basic Instinct, Universal)

#### Adding Constraints to Ternary Relationships
**Problem**: If we want "each movie has only one studio" but still track actor contracts:

**Solution**: Use binary relationship + constraint
1. Movies → Studios: One-to-one (each movie has exactly one studio)
2. Actors ↔ Movies: Many-to-many (contract without explicit studio reference)

Now the studio for any actor-movie combination is implicit through the movie-studio relationship.

### Recursive Relationships

#### Self-Referencing Entity Sets
Sometimes need to connect entities within the same entity set.

**Example**: Actors who played together
```
┌─────────┐
│ Actors  │──┐
└─────────┘  │
     ↑       │ played_with
     └───────┘
```

#### Role Labels
When the relationship direction matters, add role labels:
```
┌─────────┐
│ Actors  │──┐
└─────────┘  │
     ↑       │ 
   husband   │ wife
     └───────┘
```

#### Symmetry Issues
ER diagrams cannot enforce symmetry. If storing "Tom Cruise ↔ Nicole Kidman," the diagram doesn't automatically require "Nicole Kidman ↔ Tom Cruise."

### Relationship Attributes

#### Purpose
Add descriptive information to relationships that doesn't belong to either participating entity.

**Example**: Contract between Actor and Movie
```
┌─────────┐    contract    ┌─────────┐
│ Actors  │◊──────────────◊│ Movies  │
└─────────┘    salary      └─────────┘
```

#### Why Use Relationship Attributes?
- **Salary belongs to relationship**: Makes sense only when actor and movie are together
- **Not actor attribute**: Same actor gets different salaries for different movies  
- **Not movie attribute**: Different actors in same movie get different salaries

#### Important Constraint
**No matter how many attributes you add to a relationship, the identifying characteristic remains the same**: only the participating entities determine uniqueness.

Cannot store:
- (Arnold, Terminator, $1000)
- (Arnold, Terminator, $2000) ← **Not allowed - same entities**

#### Alternative: Convert to Entity Set
If you need to store multiple instances of the same entity combination:
```
┌─────────┐   ┌──────────┐   ┌─────────┐
│ Actors  │──→│ Contract │←──│ Movies  │
└─────────┘   └──────────┘   └─────────┘
              │ ID (key)  │
              │ Salary    │
              └──────────┘
```

**Trade-offs**:
- **Advantage**: Can store multiple contracts between same actor and movie
- **Disadvantage**: Lose automatic constraint that entity combination determines uniqueness

---

## Key Design Principles

### 1. Constraint Enforcement
- Use ER diagrams to **capture and enforce business rules**
- Think carefully about what constraints the real world requires
- Don't over-constrain (make database unusable) or under-constrain (allow invalid data)

### 2. Entity vs. Attribute Decision
- **Use entity sets** when you need to store multiple attributes about something OR when it participates in relationships
- **Use simple attributes** when you only need to store a single value and don't need to relate it to other entities

### 3. Don't Model the Entire Universe
- Focus only on data you **actually need** for your system
- Don't create entity sets for every possible real-world concept
- Example: Song authors as strings vs. separate Person entity set

### 4. Technology Independence
- ER diagrams should describe requirements independent of implementation technology
- Same diagram can be translated to relational databases, graph databases, etc.
- Translation from well-designed ER diagram to SQL should be automatic

### 5. Documentation and Communication
- ER diagrams serve as documentation that non-technical stakeholders can understand
- Help judge quality of existing database designs
- Enable reverse engineering of poorly documented databases

---

## Next Steps

### Assignment 1 (End of October)
- **Task**: Database design using ER diagrams
- **Input**: Natural language description of requirements
- **Output**: ER diagram that faithfully represents all requirements and constraints
- **Duration**: ~15 days to complete
- **Evaluation**: Quality and correctness of database design

### Upcoming Topics
- **Weak Entity Sets**: Entity sets whose key depends on other entities
- **Translation**: Converting ER diagrams to relational database schemas
- **SQL Implementation**: Implementing the designed database with actual SQL commands

### Practical Advice
- **Practice with examples**: Work through multiple design scenarios
- **Think about constraints**: Always consider what the real world requires and forbids
- **Test your design**: Try to identify potential problems by thinking through example data
- **Iterative improvement**: Database design often requires multiple refinements
