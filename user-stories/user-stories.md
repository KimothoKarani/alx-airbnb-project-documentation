# Airbnb Clone Project: Detailed User Stories & Acceptance Criteria

Below are the core user stories that capture our system’s key interactions.

---

### **Story 1: New User Registration**

> **As a** new visitor to the site,
> **I want to** create a new account using my email and a password,
> **so that** I can access the platform's features as either a guest or a host.

**Acceptance Criteria:**
- **Given** a user is on the registration page,
- **When** they enter a valid, unique email address and a strong password,
- **And** they submit the form,
- **Then** a new `User` record is created in the database with the `role` of 'guest' by default.
- **And** the user is automatically logged in and redirected to the homepage.
- **And** the system rejects registration if the email address is already in use.

---

### **Story 2: Property Listing Creation**

> **As a** property owner (Host),
> **I want to** fill out a form to create a new property listing with details like name, description, location, price, and photos,
> **so that** my property becomes visible to potential guests.

**Acceptance Criteria:**
- **Given** a logged-in user has the `role` of 'host',
- **When** they navigate to the "Create Listing" page and submit the form with all required fields (name, description, location, price),
- **Then** a new `Property` record is created and associated with their `host_id`.
- **And** they can upload multiple images for the property.
- **And** the new listing is immediately searchable on the platform.

---

### **Story 3: Guest Property Search**

> **As a** traveler (Guest),
> **I want to** search for properties by location (e.g., "Nairobi") and filter the results,
> **so that** I can easily find a suitable rental for my trip.

**Acceptance Criteria:**
- **Given** a user is on the homepage or search page,
- **When** they enter a location in the search bar,
- **Then** the system displays a paginated list of properties matching that location.
- **And** the user can further filter these results by price range, number of guests, and specific amenities.
- **And** each result in the list shows a primary photo, name, price per night, and overall rating.

---

### **Story 4: Property Booking**

> **As a** logged-in Guest,
> **I want to** select dates on a property's calendar and confirm a booking,
> **so that** I can reserve the property for my stay.

**Acceptance Criteria:**
- **Given** a guest is viewing a specific property page,
- **When** they select a valid start and end date from the availability calendar,
- **Then** the system calculates and displays the `total_price`.
- **And** upon clicking "Book", a new `Booking` record is created with a `status` of 'pending'.
- **And** the system prevents booking dates that overlap with an existing 'confirmed' booking for that property.
- **And** the user is redirected to the payment page to complete the booking.

---

### **Story 5: Payment for Booking**

> **As a** Guest who has initiated a booking,
> **I want to** securely pay the total amount using a credit card,
> **so that** my booking status changes to 'confirmed'.

**Acceptance Criteria:**
- **Given** a booking has a status of 'pending',
- **When** the user submits valid payment details via the integrated payment gateway (e.g., Stripe),
- **And** the payment is successfully processed by the gateway,
- **Then** a `Payment` record is created and linked to the `booking_id`.
- **And** the corresponding `Booking` status is updated from 'pending' to 'confirmed'.
- **And** the user receives a confirmation notification (e.g., email).

---

### **Story 6: Leaving a Property Review**

> **As a** Guest who has completed a stay,
> **I want to** be able to leave a star rating (1-5) and a written comment,
> **so that** I can share my experience with other travelers.

**Acceptance Criteria:**
- **Given** a user has a `Booking` with a status of 'completed',
- **When** they navigate to the property's page or their booking history,
- **Then** they are presented with an option to leave a review.
- **And** upon submitting a rating between 1 and 5 and a text comment, a new `Review` record is created.
- **And** the review is associated with both the user's `user_id` and the property's `property_id`.
- **And** the system does not allow reviews for bookings that are not yet 'completed'.