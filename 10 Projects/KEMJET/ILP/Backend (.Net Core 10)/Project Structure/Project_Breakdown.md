
# **Project Breakdown**

### 1. **`Kemjet.Ilp.Domain` (Core / Domain Layer):**
- This is the innermost layer. 
- It contains your core enterprise logic, entities, value objects, exceptions, and domain events. 
- It has absolutely zero dependencies on any other projects or external databases.
- The database is just a detail; business rules don't care what database you use.

### 2. **`Kemjet.Ilp.Contracts` (Application / Interface Definitions):**
- This project defines:
	- Data Transfer Objects (DTOs), 
	- requests, responses, 
	- And Interfaces (used to communicate across boundaries without exposing the actual implementations.)
### 3. **`Kemjet.Ilp.BAL` (Business Application Layer / Application Core):**
- Contains your application use cases, workflows, services, handlers (like MediatR if you use it), abstractions, and dependency injection configurations.

### 4. **`Kemjet.Ilp.DAL` (Data Access Layer / Infrastructure):**
- Handles database interactions, Entity Framework Core configurations, data repositories, or external cache mechanisms. This is an outer layer that implements the interfaces defined in the core.

### 5. **`Kemjet.Ilp.Integrations` (Infrastructure / Third-Party):** 
- Houses the connections to external systems, such as third-party APIs, payment gateways, or message queues.

### 6. **`Kemjet.Ilp.Api` & `Kemjet.Ilp.Web` & `Kemjet.Ilp.Worker` (Presentation Layer):** 
- These are the entry points to your application. 
- They expose your endpoints to consumers via REST APIs, standard web pages, or background worker processes. 
- They focus solely on handling inputs/outputs and routing requests to the application layer.

---

## About this Architecture 

- It is a Low Coupling. You change the database implementation without affecting core business rules.