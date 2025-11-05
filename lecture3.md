# DATA MINING AND KNOWLEDGE EXTRACTION - Lecture 3
**Topic:** Advanced Entity-Relationship Diagrams

---

## Quick Recap from Previous Lecture

### Entity-Relationship Diagrams Fundamentals
- **Entity Sets**: Represented by boxes containing collections of entities of the same type
  - Include entity name and attributes
  - Must specify a **key** (underlined attributes that uniquely identify entities)

- **Relationship Sets**: Represented by diamonds connecting entity sets
  - **Simple lines**: Many-to-many relationship (each entity can connect to zero or more entities on the other side)
  - **Arrow**: One-to-many relationship (entity on arrow side connects to at most one entity)
  - **Thick line**: Participation constraint (entity must be connected to one or more entities)
  - **Thick line + Arrow**: Exactly one relationship

### Multi-way Relationships
- Can connect more than two entity sets
- Key principle: **Entities participating in a relationship uniquely determine that relationship**
- Example: Movie-Star-Studio contract relationship
  - Given a specific movie, star, and studio, there can only be one unique contract
  - Cannot have duplicate relationships between the same entities

---

## Today's Advanced Concepts

## 1. Aggregation

### Problem Context
Consider a database tracking departments and projects:
- **Departments**: name, budget, dept_id (key)
- **Projects**: budget, start_date, project_id (key)
- **Sponsors relationship**: departments sponsor projects with a start_date attribute

**Challenge**: Need to track employees who supervise sponsorships.

### Why Standard Approaches Fail

#### Attempt 1: Direct Connection (Syntactically Wrong)
```
Employee → Sponsors (diamond)
```
- **Problem**: Cannot connect entity set directly to relationship set
- Violates ER diagram syntax rules

#### Attempt 2: Adding Supervisor Attribute
```
Sponsors relationship with supervisor attribute
```
- **Problems**:
  - Supervisor becomes arbitrary string (no validation)
  - Only allows one supervisor per sponsorship
  - Cannot have multiple supervisors for same sponsorship

#### Attempt 3: Ternary Relationship
```
Department-Project-Employee ternary relationship
```
- **Problem**: Arrow constraints too restrictive
- If Department → (Project, Employee), then one department can only have one unique (project, employee) pair
- Prevents multiple employees supervising same sponsorship

### Solution: Aggregation

**Aggregation** allows treating a relationship set temporarily as an entity set.

#### Notation:
- Enclose the relationship set and participating entity sets in a **dashed box**
- This "aggregated" relationship can now connect to other entity sets
- The key of the aggregated entity is the combination of keys from participating entities

#### Example:
```
[Department-Sponsors-Project] → Monitors → Employee
```

**Key of Sponsors aggregation**: (dept_id, project_id)

#### Benefits:
1. **Independent relationship**: Monitors is separate from Sponsors
2. **Additional attributes**: Can add monitoring start_date, etc.
3. **Flexible constraints**: Can specify supervision requirements
4. **Bidirectional reasoning**:
   - Employee can monitor zero or more sponsorships
   - Sponsorship can be monitored by zero or more employees

---

## 2. Subclasses (Inheritance)

### Concept
**Subclass**: A subset of entities from a parent entity set with special properties.

### Key Differences from Programming Languages
- **Database perspective**: True subset relationship
- **Programming perspective**: Different classes with inheritance

#### Database Subclasses:
- Every cartoon IS a movie (exists in both entity sets)
- Subset relationship: Cartoons ⊆ Movies

#### Programming Inheritance:
- Separate classes with shared interface
- No subset relationship between object instances

### Notation
- **Triangle symbol** pointing from subclass to superclass
- **"ISA" relationship**: "A cartoon ISA movie"
- **No diamond**: Uses triangle instead of relationship diamond

### Example: Movies Database
```
Movies (title, length) with title as key
  ↑ (ISA triangle)
Cartoons (inherits title, length)
  ↑ (ISA triangle)  
Murder_Mysteries (inherits title, length, weapon)
```

### Inheritance Properties
1. **Attribute inheritance**: Subclasses automatically inherit all parent attributes
2. **Key inheritance**: Subclasses use the same primary key as parent
3. **Subset requirement**: All subclass entities must exist in parent entity set

### Reasons for Creating Subclasses
1. **Additional attributes**: Murder mysteries need "weapon" attribute
2. **Specific relationships**: Only cartoons relate to voice actors
3. **Specialized constraints**: Different rules for different types

### Default Subclass Behavior
- **Can overlap**: Entity can belong to multiple subclasses
- **Partial coverage**: Not all parent entities need to be in subclasses
- **Tree structure**: No multiple inheritance (each subclass has one parent)

---

## 3. Keys and Candidate Keys

### Terminology
- **Super Key**: Any set of attributes that can identify entities (may include redundant attributes)
- **Candidate Key**: Minimal set of attributes that uniquely identifies entities
- **Primary Key**: The designated candidate key (underlined in diagrams)

### Minimality Requirement
A key is **minimal** if removing any attribute makes it insufficient for unique identification.

#### Example: Movies with (title, year) key
- **Minimal**: Both title and year needed
  - Title alone: Multiple movies can have same title
  - Year alone: Multiple movies released in same year
- **Not minimal**: (title, year, language) - language is redundant

### Subclass Key Inheritance
**Rule**: Subclasses inherit the primary key of their parent class.

**Rationale**: Since subclass entities must also exist in parent class, they need to be distinguishable among ALL parent entities, not just within the subclass.

---

## 4. Weak Entity Sets

### Motivation: Hotel Rooms Example
**Problem**: How to uniquely identify hotel rooms?

#### Naive Approaches and Their Problems:

1. **Room number as key**:
   - Problem: Multiple hotels can have room "101"

2. **Room number + beds as key**:
   - Problem: Still not unique across hotels

3. **Adding artificial ID**:
   - Problem: Allows duplicate representations of same physical room
   - Can store same room multiple times with different IDs

### What Actually Identifies a Room?
**Answer**: Room number + Hotel identifier

But hotel information isn't directly stored in room entity!

### Weak Entity Solution

#### Definition
**Weak Entity Set**: Cannot exist independently; requires a "supporting entity" for identification.

#### Notation
- **Double-lined box** around weak entity name
- **Double-lined diamond** for supporting relationship
- **Arrow with thick line** pointing to supporting entity

#### Example: Rooms and Hotels
```
Hotel (reg_number, name, address) [reg_number is key]
    ↓ (thick line + arrow)
  Contains (double diamond)
    ↓
Rooms (room_number, beds) [weak entity - double box]
```

**Key of Room**: (room_number, reg_number)
- Partial key: room_number (from room attributes)
- Supporting key: reg_number (from hotel)

### Weak Entity Properties
1. **Cannot exist alone**: Must have supporting entity
2. **Cascade deletion**: If supporting entity deleted, weak entities must be deleted
3. **Mandatory relationship**: Must have thick line + arrow to supporting entity
4. **Composite key**: Combines own attributes with supporting entity's key

### Multiple Supporting Entities
- Possible but uncommon
- All supporting relationships must be thick line + arrow
- Key includes keys from all supporting entities

---

## 5. Converting Relationships to Entity Sets

### When and Why?
Sometimes need to convert relationship sets to entity sets to model complex scenarios.

#### Example: Ternary Relationship → Weak Entity
```
Original: Movie-Actor-Studio (ternary contracts relationship)
Convert to: Contract (weak entity) supported by Movie, Actor, Studio
```

### Using Weak Entities for Relationship Conversion
**Key insight**: Relationship semantics must be preserved.

#### Contract Example:
- **Original**: (movie, actor, studio) uniquely identifies contract
- **Converted**: Contract is weak entity with key = (movie_key, actor_key, studio_key)
- **No additional attributes**: Key consists entirely of supporting entity keys

### Why Use Weak Entities?
Prevents artificial ID problems:
- With artificial ID: Can create multiple "different" contracts for same (movie, actor, studio)
- With weak entity: Preserves original relationship uniqueness constraint

---

## Design Principles and Best Practices

### 1. Avoid Redundancy
**Primary goal**: Don't store the same information in multiple places.

#### Bad Design Example:
```
Movies (movie_name, studio_name, studio_address)
```

**Problems**:
- Studio information repeated for every movie
- Must update studio address in multiple places
- Risk of inconsistencies

#### Good Design:
```
Movies (movie_name) → Films → Studios (studio_name, address)
```

### 2. Enforce Constraints Through Design
- Use arrows and participation constraints
- Don't rely on application logic alone
- Make violations impossible, not just discouraged

### 3. Entity Set vs. Attribute Decision

#### Create Entity Set When:
1. **Multiple attributes**: More than just a name/identifier
2. **Relationships**: Needs to connect to other entities
3. **Many-to-many**: Multiple entities can relate to it

#### Use Attribute When:
1. **Simple value**: Just a string or number
2. **No relationships**: Doesn't connect to other entities
3. **One-to-many**: Each parent has at most one

#### Example Analysis:
```
Option 1: Movies → Films → Studios (name, address)
Option 2: Movies (movie_name, studio_name)
```

**Choose Option 1 when**:
- Studios have multiple attributes (address)
- Studios relate to other entities
- One studio produces multiple movies

**Choose Option 2 when**:
- Studio is just a name
- No other studio information needed
- No other relationships for studios

### 4. Minimize Weak Entity Usage
- **Prefer**: Finding natural unique identifiers
- **Look for**: Government IDs, registration numbers, official identifiers
- **Use weak entities**: Only when no natural unique identifier exists

#### Example: Room Registration
If rooms have government inspection numbers, use those instead of weak entity design.

---

## Summary of ER Diagram Components

### Basic Components
1. **Entity Sets**: Collections of similar entities
2. **Relationship Sets**: Connections between entity sets
3. **Attributes**: Properties of entities/relationships
4. **Keys**: Unique identifiers (primary, candidate, super)

### Advanced Components
5. **Aggregation**: Treating relationships as entities
6. **Subclasses**: Specialized entity subsets with inheritance
7. **Weak Entities**: Entities requiring supporting entities for identification

### Design Guidelines
- Minimize redundancy
- Enforce constraints through structure
- Choose appropriate abstraction level
- Use weak entities sparingly
- Preserve real-world semantics

