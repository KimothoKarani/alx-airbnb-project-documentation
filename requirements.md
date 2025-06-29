# ALX Airbnb Clone: Backend Feature Requirement Specifications

**Project:** `alx-airbnb-project-documentation`

**Author:** Simon Kimotho

**Date:** June 29, 2025

---

## 1. Introduction

This document provides detailed technical and functional specifications for the key backend features of the ALX Airbnb Clone project. It is intended for backend developers and serves as the primary technical blueprint for API implementation.

Each feature specification includes details on its API endpoints, expected request/response payloads, validation rules, business logic, and performance criteria.

---

## 2. Feature: User Authentication & Management

This feature covers user registration, login, and secure session management using JSON Web Tokens (JWT).

### 2.1. Endpoint: `POST /api/auth/register`

* **Description:** Creates a new user account.
* **Request Body (Input):**
    ```json
    {
      "firstName": "Grace",
      "lastName": "Wambui",
      "email": "grace.wambui@example.com",
      "password": "a_strong_password_123"
    }
    ```
* **Validation Rules:**
    * `firstName`, `lastName`, `email`, `password` are all required fields.
    * `email` must be a valid email format and must be unique in the `User` table.
    * `password` must be at least 8 characters long.
* **Success Response (Output):** `201 Created`
    ```json
    {
      "message": "User registered successfully",
      "user": {
        "userId": "a1b2c3d4-e5f6-7890-1234-567890abcdef",
        "firstName": "Grace",
        "lastName": "Wambui",
        "email": "grace.wambui@example.com",
        "role": "guest"
      },
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJhMWIyYzNkNC1lNWY2LTc4OTAtMTIzNC01Njc4OTBhYmNkZWYiLCJpYXQiOjE3MTk3NjYzODZ9.some_signature"
    }
    ```
* **Error Responses:**
    * `400 Bad Request`: If any validation rule fails (e.g., missing field, invalid email).
    * `409 Conflict`: If the email address already exists.
* **Performance Criteria:** Response time should be under 200ms.

### 2.2. Endpoint: `POST /api/auth/login`

* **Description:** Authenticates a user and returns a JWT for session management.
* **Request Body (Input):**
    ```json
    {
      "email": "grace.wambui@example.com",
      "password": "a_strong_password_123"
    }
    ```
* **Validation Rules:**
    * `email` and `password` are required.
    * `email` must be a valid email format.
* **Business Logic:**
    1.  Find the user by their email address.
    2.  Compare the provided password with the stored `password_hash` using bcrypt.
* **Success Response (Output):** `200 OK`
    ```json
    {
      "message": "Login successful",
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJhMWIyYzNkNC1lNWY2LTc4OTAtMTIzNC01Njc4OTBhYmNkZWYiLCJpYXQiOjE3MTk3NjYzODZ9.some_signature"
    }
    ```
* **Error Responses:**
    * `401 Unauthorized`: If the email does not exist or the password does not match.
* **Performance Criteria:** Response time should be under 150ms.

---

## 3. Feature: Property Listings Management

This feature covers the creation and retrieval of property listings. It is restricted to users with the 'host' role.

### 3.1. Endpoint: `POST /api/properties`

* **Description:** Creates a new property listing.
* **Authorization:** Requires a valid JWT. The user's role must be `host`.
* **Request Body (Input):**
    ```json
    {
      "name": "Serene Studio in Kilimani",
      "description": "A quiet and modern studio apartment...",
      "location": "Kilimani, Nairobi",
      "price_per_night": 6500.00
    }
    ```
* **Validation Rules:**
    * `name`, `description`, `location`, `price_per_night` are required.
    * `price_per_night` must be a positive number.
* **Success Response (Output):** `201 Created`
    ```json
    {
      "propertyId": "f6a7b8c9-d0e1-2345-6789-012345f01234",
      "hostId": "a1b2c3d4-e5f6-7890-1234-567890abcdef",
      "name": "Serene Studio in Kilimani",
      "description": "A quiet and modern studio apartment...",
      "location": "Kilimani, Nairobi",
      "price_per_night": 6500.00,
      "created_at": "2025-06-29T20:30:00Z"
    }
    ```
* **Error Responses:**
    * `401 Unauthorized`: If no valid JWT is provided.
    * `403 Forbidden`: If the user's role is not `host`.
    * `400 Bad Request`: If validation fails.
* **Performance Criteria:** Response time under 250ms.

---

## 4. Feature: Booking System

This feature covers the process of a guest booking a property.

### 4.1. Endpoint: `POST /api/bookings`

* **Description:** Creates a new booking for a property.
* **Authorization:** Requires a valid JWT.
* **Request Body (Input):**
    ```json
    {
      "propertyId": "f6a7b8c9-d0e1-2345-6789-012345f01234",
      "startDate": "2025-08-20",
      "endDate": "2025-08-27"
    }
    ```
* **Validation Rules:**
    * `propertyId`, `startDate`, `endDate` are required.
    * `startDate` and `endDate` must be valid dates.
    * `endDate` must be after `startDate`.
* **Business Logic:**
    1.  Verify that the `propertyId` exists and is available for booking.
    2.  Check the `Bookings` table to ensure the requested date range (`startDate` to `endDate`) does not overlap with any existing, `confirmed` bookings for this specific `propertyId`.
    3.  Calculate the `total_price` based on `price_per_night` and the number of nights.
* **Success Response (Output):** `201 Created`
    ```json
    {
      "bookingId": "c9d0e1f2-a3b4-5678-9012-345678234567",
      "propertyId": "f6a7b8c9-d0e1-2345-6789-012345f01234",
      "userId": "c3d4e5f6-a7b8-9012-3456-789012cdef01",
      "startDate": "2025-08-20",
      "endDate": "2025-08-27",
      "total_price": 45500.00,
      "status": "pending",
      "created_at": "2025-06-29T20:45:00Z"
    }
    ```
* **Error Responses:**
    * `401 Unauthorized`: If the user is not logged in.
    * `400 Bad Request`: If date validation fails.
    * `404 Not Found`: If the `propertyId` does not exist.
    * `409 Conflict`: If the dates are already booked.
* **Performance Criteria:** Response time under 300ms, as it involves a potentially complex database query.
