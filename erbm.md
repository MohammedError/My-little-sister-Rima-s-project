# HBnB - UML Documentation

## Contents
- [Introduction](#introduction)
- [1. High-Level Architecture](#1-high-level-architecture)
- [2. Business Logic Layer](#2-business-logic-layer)
- [3. API Interaction Flow](#3-api-interaction-flow)
- [Authors](#authors)

## Introduction

        The HBnB project is a simplified property rental platform inspired by modern accommodation booking systems. The system allows users to create and manage accounts, list properties, submit reviews, and associate amenities with places. It is designed to demonstrate real-world software engineering concepts such as layered architecture, API-driven communication, and object-oriented design.

        The purpose of this technical document is to define the architecture and design of the HBnB system and to serve as a reference during the implementation phase. It helps developers understand how system components interact, how business rules are applied, and how data flows across the application, ensuring consistency and alignment with project requirements.

        This document covers the system architecture, core business entities, and API interaction flows using UML diagrams. It includes high-level architecture diagrams, class diagrams, and sequence diagrams that provide a clear blueprint of the system before development begins.

## 1. High-Level Architecture

**1.1 High-Level Package Diagram**

![High-Level Package Diagram](https://github.com/Fai-Web-Lab/holbertonschool-hbnb/blob/8bc9eb35a2cf7e4675e608da611bb9e458999d48/part1/_High-LevelPackageDiagram.drawio.png)

**1.2 Explanatory Notes**
**1.2.1 Purpose of the Diagram**

This package diagram provides a **high-level visual representation** of the HBnB Evolution application's three-layer architecture. It serves multiple purposes:

- **Architectural Visualization:** Clearly illustrates the separation of concerns between presentation, business logic, and persistence layers

- **Communication Flow:** Demonstrates how data and requests move through the system using the Facade Pattern

- **Component Organization:** Shows how related classes are grouped into packages for better maintainability

- **Design Pattern Implementation:** Highlights the use of Facade Pattern to simplify complex subsystem interactions

- **Onboarding Tool:** Helps new developers understand the system structure without diving into code

    The diagram acts as a **living documentation** that bridges the gap between conceptual design and actual implementation.

**1.2.2 Key Components and Classes**

| Layer | Components | Responsibilities |
|-------|------------|-----------------|
| **Presentation Layer** | Services, APIs Endpoints | Handles user requests and responses. Does not contain business logic or direct database access. |
| **Business Logic Layer** | Facade, Models (User, Place, Review, Amenity, BaseModel) | Core business logic, validation, and coordination between layers. The Facade provides a simplified interface for the Presentation Layer. |
| **Persistence Layer** | Database, Repository / Storage | Handles data storage and retrieval (CRUD operations). No direct interaction with the Presentation Layer. |

**1.2.3 Connections Between Layers**
1. **Presentation Layer → Business Logic Layer**
    Connection: API/Services → Facade
    Type: Service Invocation / Method Call
    Purpose: Presentation layer calls facade methods to execute business operations
    Example: facade.registerUser(userData)

2. **Business Logic Layer Internal**
    Connection: Facade → Models
    Type: Business Logic Execution
    Purpose: Facade creates and manipulates domain entities (User, Place, etc.)
    Example: User user = new User(data)

3. **Business Logic Layer → Persistence Layer**
    Connection: Models → Repositories
    Type: Data Persistence / CRUD Operations
    Purpose: Entities save/load data through repositories
    Example: userRepository.save(user)

4. **Persistence Layer → Database**
    Connection: Repositories → Database
    Type: Database Operations / SQL Execution
    Purpose: Repositories execute SQL queries on the database
    Example: INSERT INTO users (...) VALUES (...)

**1.2.4 Design Decisions**

- Three-Layer Architecture: Separates UI, business rules, and data for maintainability and independent development.

- Facade Pattern: Single entry point (Facade) simplifies client interaction and hides internal complexity.

- Repository Pattern: Abstracts database access, enabling easy switching between databases and better testing.

- Domain Models: Business logic resides in entities (User, Place, etc.), keeping it centralized and organized.

**1.2.5 Architecture Integration**

This diagram represents the core structure of HBnB:

- Flow:
    Client → Presentation (API/Services) → Business Logic (Facade → Models) → Persistence (Repositories → Database)

- Role in Overall System:
    Foundation for all features (user mgmt, place listing, reviews)
    Ensures clean separation for scalability
    Follows proven enterprise patterns
    Enables parallel team work on different layers

## 2. Business Logic Layer

**2.1 Detailed Class Diagram**
**Tool used:** Mermaid.js

```mermaid
classDiagram
    %% Base Class for common attributes
    class BaseModel {
        +UUID id
        +DateTime created_at
        +DateTime updated_at
        +save()
        +to_dict()
    }

    %% User Entity
    class User {
        +String first_name
        +String last_name
        +String email
        +String password
        +Boolean is_admin
        +register()
        +update_profile()
        +delete()
    }

    %% Place Entity
    class Place {
        +String title
        +String description
        +Float price
        +Float latitude
        +Float longitude
        +String owner_id
        +List amenities
        +create()
        +update()
        +delete()
        +list()
    }

    %% Review Entity
    class Review {
        +Integer rating
        +String comment
        +String user_id
        +String place_id
        +create()
        +update()
        +delete()
        +list_by_place()
    }

    %% Amenity Entity
    class Amenity {
        +String name
        +String description
        +create()
        +update()
        +delete()
        +list()
    }

    %% Relationships and Inheritance
    BaseModel <|-- User
    BaseModel <|-- Place
    BaseModel <|-- Review
    BaseModel <|-- Amenity

    User "1" -- "0..*" Place : Owns
    User "1" -- "0..*" Review : Writes
    Place "1" -- "0..*" Review : Receives
    Place "*" -- "*" Amenity : Has
2.2 Explanatory Notes2.2.1 Purpose of the DiagramThis Class Diagram serves as the detailed blueprint for the Business Logic Layer. It defines the static structure of the system by mapping out entities, attributes, methods, and relationships. It ensures that all developers adhere to a strict data structure during the implementation of the core application logic.2.2.2 Key Components and EntitiesEntityDescriptionResponsibilitiesBaseModelThe Abstract Base ClassImplements the DRY principle. Automatically manages unique IDs (UUID4) and audit timestamps (created_at, updated_at) for all system objects.UserThe System ActorRepresents registered users. Encapsulates authentication data (password, email) and authorization roles (is_admin).PlaceThe Core AssetRepresents the properties available for rent. Stores business data like price and location, serving as the central hub for interactions.ReviewThe Feedback EntityLinks a specific User to a specific Place with a rating and comment, enforcing valid feedback loops.AmenityThe Feature EntityRepresents reusable features (e.g., WiFi, Pool). Designed to be linked to multiple places to avoid data redundancy.2.2.3 Relationships and LogicInheritance (<|--)Logic: All entities (User, Place, Review, Amenity) inherit from BaseModel.Purpose: Guarantees that every object in the system has a unique identifier and creation timestamp without rewriting code.Associations (--)User owns Place (1-to-Many): A strict ownership rule; a Place cannot exist without an Owner.User writes Review (1-to-Many): Allows users to submit multiple reviews across the platform.Place receives Review (1-to-Many): Aggregates feedback for a specific property.Place has Amenities (Many-to-Many): Provides flexibility, allowing a single Amenity (like "Internet") to be associated with thousands of Places efficiently.2.2.4 Design DecisionsAbstraction (BaseModel): Centralizing ID and timestamp logic reduces errors and ensures consistency across the database.Separation of Data: Keeping Amenity as a separate entity rather than a simple string list allows for better filtering and database normalization.Role Management: Including is_admin directly in the User model simplifies the initial authorization implementation without needing complex Role-Based Access Control (RBAC) tables at this stage.3. API Interaction Flow3.1. User Registration:3.1.1. Data Flow and Interactions:1- User → Browser:New user enters registration data (username, email, password).2- Browser → API Layer:Sends POST /register request.3- API → Validation/Business Logic:Checks for required fields, password complexity, and whether the user already exists.4- Business Logic → Database:If validation passes, the new user is persisted.5- Database → Business Logic → API → Browser → User:Confirmation (account created) or error (user exists) returns to the user.3.1.2. Explanatory Notes1- Purpose of the Diagram:It describes how new users register and how the system prevents duplicate accounts.2- Key Components Involved:UserBrowserPresentation Layer (API)Auth / Validation LogicBusiness LogicPersistence Layer (Database)3- Design Decisions and Rationale:Input validation occurs before database interaction.Duplicate user checking prevents data conflicts.Uses ALT flow for existing vs new users.4- How It Fits into Overall Architecture:It represents the entry point to the system, ensuring only valid and unique users are created.3.2. Place Creation:3.2.1. Data Flow and Interactions:1- User → Browser:User submits a form with new place details.2- Browser → API Layer:Sends a POST /places request with JSON payload.3- API → Authentication Service:Before processing, API validates the JWT token to ensure the user is authenticated.4- API → Business Logic Layer:Once authenticated, the data is sent to business logic. Here, the system validates fields (e.g., name, location, price).5- Business Logic → Database:Valid data is persisted into the database.6- Database → Business Logic → API → Browser → User:Confirmation of creation (success/failure) flows back up the chain, informing the user.3.2.2. Explanatory Notes1- Purpose of the Diagram:It shows how a new place listing is created in the system, including authentication, validation, and database persistence.2- Key Components Involved:UserBrowserPresentation Layer (API)Authentication Service (Auth)Business LogicPersistence Layer (DB)3- Design Decisions and Rationale:JWT validation ensures only authenticated users can create places.Business layer validates place data before saving.Database layer is isolated for data integrity and security.4- How It Fits into Overall Architecture:It represents a write operation and highlights secure resource creation using authentication and validation layers.3.3. Review Submission:3.3.1. Data Flow and Interactions:1- User → Browser:User writes a review for a place.2- Browser → API Layer:Sends POST /reviews request.3- API → Auth Service:API checks if user is authenticated (ALT flow logic: authenticated vs unauthenticated).4- API → Business Logic:If authenticated, business logic validates that the place exists and the review data is correct.5- Business Logic → Database:Review is inserted into the database.6- Database → Business Logic → API → Browser → User:Success message or error returns to the user.3.3.2. Explanatory Notes1- Purpose of the Diagram:It explains how users submit reviews while handling authentication scenarios (authenticated vs unauthenticated users).2- Key Components Involved:UserBrowserPresentation Layer (API)Auth ServiceBusiness Logicpersistence Layer (DB)3- Design Decisions and Rationale:Uses ALT fragment logic to handle authentication states.Prevents unauthorized review submissions.Business logic verifies place existence before saving review.4- How It Fits into Overall Architecture:It supports data integrity and security enforcement, ensuring reviews are linked to valid users and places.3.4. Fetching a List of Places:3.4.1. Data Flow and Interactions:1- User → Browser:The user inputs search criteria (e.g., city = Riyadh) in the UI. This data is packaged in an HTTP request.2- Browser → API (Presentation Layer):The browser sends the request to the API endpoint (GET /places). The API acts as a mediator, receiving the request and parsing parameters.3- API → Business Logic Layer:The API forwards the parsed filters to the business logic, which prepares a query. The business logic may apply additional filtering or validation to avoid sending unsafe queries directly to the database.4- Business Logic → Database (Persistence Layer):The query is executed against the database, fetching matching places.5- Database → Business Logic → API → Browser → User:Results flow back up the stack: the database returns the data to the business logic, which may format it; the API sends it as an HTTP response to the browser; finally, the user sees the list.3.4.2. Explanatory Notes1- Purpose of the Diagram:It illustrates how the system retrieves a filtered list of places based on user search criteria (e.g., city = Riyadh). It demonstrates the read operation flow from the user interface down to the database and back.2- Key Components Involved:UserBrowserPresentation Layer (API)Business LogicPersistence Layer (DB)3- Design Decisions and Rationale:Layer separation ensures maintainability and scalability.Business logic handles filtering to avoid database coupling with UI.API acts as the contract between frontend and backend.4- How It Fits into Overall Architecture:It represents a read/query operation and demonstrates how the layered architecture supports safe and scalable data retrieval.AuthorsLayla AlShehri Mohammed AlOufi Fai AlSharekh
