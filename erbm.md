# HBnB - UML Documentation

## Contents
- [Introduction](#introduction)
- [1. High-Level Architecture](#1-high-level-architecture)
- [2. Business Logic Layer](#2-business-logic-layer)
- [3. API Interaction Flow](#3-api-interaction-flow)
- [Authors](#authors)

---

## Introduction

The HBnB project is a simplified property rental platform inspired by modern accommodation booking systems. The system allows users to create and manage accounts, list properties, submit reviews, and associate amenities with places. It is designed to demonstrate real-world software engineering concepts such as layered architecture, API-driven communication, and object-oriented design.

The purpose of this technical document is to define the architecture and design of the HBnB system and to serve as a reference during the implementation phase. It helps developers understand how system components interact, how business rules are applied, and how data flows across the application, ensuring consistency and alignment with project requirements.

---

## 1. High-Level Architecture

### 1.1 High-Level Package Diagram

![High-Level Package Diagram](https://github.com/Fai-Web-Lab/holbertonschool-hbnb/blob/8bc9eb35a2cf7e4675e608da611bb9e458999d48/part1/_High-LevelPackageDiagram.drawio.png)

### 1.2 Explanatory Notes

#### 1.2.1 Purpose of the Diagram
This package diagram provides a **high-level visual representation** of the HBnB Evolution application's three-layer architecture. It serves multiple purposes:
- **Architectural Visualization:** Clearly illustrates the separation of concerns between presentation, business logic, and persistence layers.
- **Communication Flow:** Demonstrates how data and requests move through the system using the Facade Pattern.
- **Component Organization:** Shows how related classes are grouped into packages for better maintainability.

#### 1.2.2 Key Components and Classes

| Layer | Components | Responsibilities |
|-------|------------|-----------------|
| **Presentation Layer** | Services, APIs Endpoints | Handles user requests and responses. Does not contain business logic or direct database access. |
| **Business Logic Layer** | Facade, Models (User, Place, etc.) | Core business logic, validation, and coordination. The Facade provides a simplified interface for the Presentation Layer. |
| **Persistence Layer** | Database, Repository | Handles data storage and retrieval (CRUD operations). No direct interaction with the Presentation Layer. |

#### 1.2.3 Connections Between Layers
1. **Presentation Layer → Business Logic Layer** (Service Invocation): API calls facade methods (e.g., `facade.registerUser()`).
2. **Business Logic Layer Internal**: Facade manipulates domain entities (User, Place).
3. **Business Logic Layer → Persistence Layer**: Entities save/load data through repositories.
4. **Persistence Layer → Database**: Repositories execute SQL queries.

---

## 2. Business Logic Layer

### 2.1 Detailed Class Diagram
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
2.2 Explanatory Notes
2.2.1 Key Entities Breakdown
BaseModel (Abstraction): Acts as the parent class for all entities to enforce the DRY principle. It manages unique IDs (UUID) and audit timestamps.

User: Represents the system's actors. Includes is_admin for role management.

Place: The core asset (properties). Stores business data like price and location.

Review: Links a User to a Place with a rating and comment.

Amenity: Represents reusable features (WiFi, Pool) associated with multiple places.

2.2.2 Relationships
Inheritance: All entities inherit from BaseModel.

Associations:

User owns Place: 1-to-Many.

Place has Amenities: Many-to-Many.

User writes Review: 1-to-Many.

3. API Interaction Flow
3.1 User Registration
Data Flow:

User → Browser: Enters registration data.

Browser → API: Sends POST /register.

API → Business Logic: Validates input and checks for duplicates.

Business Logic → Database: Persists new user if valid.

Return: Confirmation or error sent back to User.

3.2 Place Creation
Data Flow:

User → Browser: Submits new place details.

Browser → API: Sends POST /places.

API → Auth Service: Validates JWT token.

API → Business Logic: Validates place data (price, location).

Business Logic → Database: Persists valid data.

Return: Success/Failure message.

3.3 Review Submission
Data Flow:

User → Browser: Writes a review.

Browser → API: Sends POST /reviews.

API → Auth Service: Checks if user is authenticated.

API → Business Logic: Verifies Place exists and validates review content.

Business Logic → Database: Inserts review.

Return: Success message.

3.4 Fetching a List of Places
Data Flow:

User → Browser: Inputs search criteria (e.g., City).

Browser → API: Sends GET /places with parameters.

API → Business Logic: Prepares query based on filters.

Business Logic → Database: Executes query.

Database → Business Logic → API → Browser: Returns list of places to the user.

Authors
Fai AlSharekh

Layla AlShehri

Mohammed Aloufi
