# E-Commerce Backend System

This project is a backend implementation of an e-commerce platform built using Spring Boot. It covers the complete flow starting from user authentication to placing orders and completing payments using PayPal (sandbox).

The goal of this project was not just to make APIs work, but to design the system in a way that reflects real-world backend architecture with proper layering, security, and extensibility.

---

## How the System Works (High-Level)

A user registers and logs in to receive a JWT token. Using this token, they can interact with secured APIs such as browsing products, managing their cart, placing orders, and making payments.

Once an order is placed, a PayPal payment session is created. The user completes the payment on PayPal, and the backend captures the payment and updates the order status accordingly.

---

## Running the Project

To run the project locally:

1. Clone the repository
2. Configure your `application.properties` file with:

   * database credentials
   * PayPal sandbox credentials
3. Run the Spring Boot application
4. Use Postman to test the APIs

Note: PayPal credentials are intentionally not included for security reasons.

---

## Authentication Flow

The system uses JWT-based authentication.

A user first registers using `/auth/register`, and then logs in via `/auth/login`. On successful login, a JWT token is returned. This token must be included in the `Authorization` header for all secured endpoints.

---

## API Overview

Instead of listing everything mechanically, here’s how the APIs are grouped based on real usage.

---

### Product Management

These APIs allow creation and management of products in the system.

* `POST /products` → creates a new product
* `GET /products` → fetches all products
* `GET /products/{id}` → fetches a specific product
* `PUT /products/{id}` → updates product details
* `DELETE /products/{id}` → removes a product

These APIs are useful for inventory handling and product catalog management.

---

### Cart Management

The cart is user-specific and allows temporary storage of selected items.

* `POST /cart/add` → adds a product to the cart
* `GET /cart` → retrieves current cart
* `DELETE /cart/remove` → removes an item

Internally, the system ensures that users can only access their own cart.

---

### Order Processing

Orders are created from the cart.

* `POST /orders` → converts cart into an order
* `GET /orders` → retrieves all orders of the user

When an order is created, the system calculates the total price and stores individual order items.

---

### Payment Integration (PayPal)

This is where the system moves closer to real-world behavior.

* `POST /payments/paypal/{orderId}`
  Creates a PayPal order and returns an approval URL.

* `GET /payments/success`
  Triggered after successful payment. Captures payment and updates order status.

* `GET /payments/cancel`
  Handles payment cancellation.

The backend stores the PayPal order ID and maps it with the internal order to ensure correct status updates.

---

## Database Design (Conceptual)

The database is structured around core e-commerce entities.

A `User` owns a `Cart` and can have multiple `Orders`.
Each `Cart` contains multiple `CartItems`, each linked to a `Product`.
Similarly, an `Order` contains multiple `OrderItems`.

The important relationships are:

* One user → many orders
* One order → many order items
* One cart → many cart items
* Each item always references a product

Additionally, the order stores a `paypalOrderId` to connect external payment with internal data.

---

## Architecture

The project follows a layered architecture:

* Controller layer handles HTTP requests
* Service layer contains business logic
* Repository layer interacts with the database

This separation keeps the code clean and makes it easier to test and extend.

---

## Error Handling

A global exception handler is used to return consistent error responses.
Instead of raw stack traces, the APIs return meaningful messages with proper HTTP status codes.

---

## Testing

Basic unit tests are written using Spring Boot Test and Mockito.
The focus was on testing service-layer logic such as order retrieval and payment updates.

---

## Key Design Decisions

A few important choices made during development:

* JWT was used for stateless authentication
* PayPal sandbox was integrated instead of mocking payments
* Service layer was strictly used to avoid direct repository access from controllers
* DTOs and exception handling were added to keep APIs clean

---

## Final Note

This project is designed to resemble a real backend system rather than a simple CRUD application.
It demonstrates how different components like authentication, data modeling, and external integrations come together in a structured way.

---
