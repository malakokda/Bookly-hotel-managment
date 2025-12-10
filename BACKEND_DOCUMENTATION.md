# Hotel Booking System - Backend Documentation

## Overview
This is a full-stack hotel booking application built with ASP.NET Core MVC, SQL Server, and Stripe payment integration. The system supports two types of users: **Customers** (who book hotels) and **Hotel Managers** (who manage their hotel through a dashboard).

---

## Database Models

### 1. **User** (Base Authentication Model)
- **Purpose**: Stores user authentication and basic profile information
- **Key Fields**:
  - `UserId` (Primary Key)
  - `Email` (Unique, required)
  - `PasswordHash` (Hashed password)
  - `FirstName`, `LastName`
  - `PhoneNumber`
  - `UserType` ("Customer" or "HotelManager")
  - `CreatedAt`
- **Relationships**:
  - One-to-One with `Customer`
  - One-to-One with `HotelManager`

### 2. **Customer**
- **Purpose**: Extended profile for customers who book hotels
- **Key Fields**:
  - `CustomerId` (Primary Key)
  - `UserId` (Foreign Key to User)
  - `Address`, `City`, `Country`, `PostalCode`
- **Relationships**:
  - One-to-One with `User`
  - One-to-Many with `Booking`

### 3. **HotelManager**
- **Purpose**: Links a user to their managed hotel
- **Key Fields**:
  - `HotelManagerId` (Primary Key)
  - `UserId` (Foreign Key to User)
  - `HotelId` (Foreign Key to Hotel)
- **Relationships**:
  - One-to-One with `User`
  - Many-to-One with `Hotel`

### 4. **Hotel**
- **Purpose**: Stores hotel information
- **Key Fields**:
  - `HotelId` (Primary Key)
  - `Name`, `Description`
  - `Address`, `City`, `Country`, `PostalCode`
  - `PhoneNumber`, `Email`
  - `ImageUrl`
  - `Rating`, `StarRating`
  - `IsActive` (for soft delete)
  - `CreatedAt`
- **Relationships**:
  - One-to-Many with `Room`
  - One-to-Many with `HotelManager`
  - One-to-Many with `Booking`

### 5. **Room**
- **Purpose**: Stores room information for each hotel
- **Key Fields**:
  - `RoomId` (Primary Key)
  - `HotelId` (Foreign Key to Hotel)
  - `RoomNumber`
  - `RoomType` (e.g., "Single", "Double", "Suite")
  - `PricePerNight`
  - `Description`
  - `MaxOccupancy`
  - `ImageUrl`
  - `IsAvailable`
  - Amenities: `HasWifi`, `HasAirConditioning`, `HasTV`, `HasMiniBar`
- **Relationships**:
  - Many-to-One with `Hotel`
  - One-to-Many with `Booking`

### 6. **Booking**
- **Purpose**: Stores customer bookings
- **Key Fields**:
  - `BookingId` (Primary Key)
  - `CustomerId` (Foreign Key to Customer)
  - `HotelId` (Foreign Key to Hotel)
  - `RoomId` (Foreign Key to Room)
  - `CheckInDate`, `CheckOutDate`
  - `NumberOfGuests`
  - `TotalAmount` (calculated: PricePerNight × NumberOfNights)
  - `Status` ("Pending", "Confirmed", "Cancelled", "Completed")
  - `CreatedAt`, `ConfirmedAt`
- **Relationships**:
  - Many-to-One with `Customer`
  - Many-to-One with `Hotel`
  - Many-to-One with `Room`
  - One-to-One with `Payment`

### 7. **Payment**
- **Purpose**: Stores payment information from Stripe
- **Key Fields**:
  - `PaymentId` (Primary Key)
  - `BookingId` (Foreign Key to Booking)
  - `StripePaymentIntentId` (Stripe payment intent ID)
  - `Amount`, `Currency`
  - `Status` ("Pending", "Succeeded", "Failed", "Refunded")
  - `PaymentDate`
  - `StripeResponse` (JSON response from Stripe)
- **Relationships**:
  - One-to-One with `Booking`

---

## MVC Controllers

### 1. **AccountController**
**Purpose**: Handles user authentication and registration

**Actions**:
- `Register()` (GET/POST) - User registration for both Customers and Hotel Managers
- `Login()` (GET/POST) - User login with email and password
- `Logout()` - Clears session and logs out user
- `Profile()` - Displays user profile with booking history (for customers)

**Key Features**:
- Password hashing using SHA256
- Session management
- Redirects Hotel Managers to Dashboard, Customers to Home

---

### 2. **HotelsController**
**Purpose**: Handles hotel search and viewing hotel details

**Actions**:
- `Index()` - Displays list of hotels with optional search filters
- `Details(int id, DateTime? checkIn, DateTime? checkOut, int? guests)` - Shows hotel details with available rooms
- `Search()` (GET/POST) - Hotel search form

**Key Features**:
- Search by city, price range, dates, and number of guests
- Availability checking based on existing bookings
- Filters out inactive hotels
- Shows only available rooms for selected dates

---

### 3. **BookingsController**
**Purpose**: Handles booking creation and management

**Actions**:
- `Create(int roomId, DateTime checkIn, DateTime checkOut, int guests)` (GET/POST) - Creates a new booking
- `MyBookings()` - Displays all bookings for the logged-in customer
- `Cancel(int id)` - Cancels a booking

**Key Features**:
- Validates room availability before creating booking
- Calculates total amount based on nights and room price
- Creates booking with "Pending" status (requires payment)
- Redirects to payment page after booking creation

---

### 4. **PaymentsController**
**Purpose**: Handles Stripe payment processing

**Actions**:
- `Create(int bookingId)` - Displays payment page with Stripe integration
- `ConfirmPayment(int bookingId, string paymentIntentId)` (POST) - Confirms payment with Stripe
- `Success(int bookingId)` - Payment success page

**Key Features**:
- Creates Stripe Payment Intent
- Stores payment information in database
- Updates booking status to "Confirmed" after successful payment
- Handles payment verification

---

### 5. **DashboardController**
**Purpose**: Hotel Manager dashboard (only accessible to Hotel Managers)

**Actions**:
- `Index()` - Dashboard overview with statistics
- `Bookings()` - View all bookings for the manager's hotel
- `Rooms()` - View all rooms for the manager's hotel
- `UpdateBookingStatus(int bookingId, string status)` (POST) - Update booking status

**Key Features**:
- Only accessible to Hotel Managers
- Shows statistics: total bookings, confirmed bookings, pending bookings, total rooms, available rooms
- Displays recent bookings
- Managers can only see bookings for their assigned hotel

---

## Step-by-Step Backend Implementation

### Step 1: Install Required NuGet Packages
```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet add package Stripe.net
```

### Step 2: Configure Database Connection
1. Update `appsettings.json` with your SQL Server connection string:
```json
"ConnectionStrings": {
  "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=HotelBookingDB;Trusted_Connection=True;MultipleActiveResultSets=true"
}
```

2. For production, use a full SQL Server connection string:
```
Server=YOUR_SERVER;Database=HotelBookingDB;User Id=YOUR_USER;Password=YOUR_PASSWORD;
```

### Step 3: Configure Stripe
1. Sign up at https://stripe.com
2. Get your API keys from the Stripe Dashboard
3. Update `appsettings.json`:
```json
"Stripe": {
  "PublishableKey": "pk_test_your_publishable_key_here",
  "SecretKey": "sk_test_your_secret_key_here"
}
```

### Step 4: Create Database Migration
```bash
dotnet ef migrations add InitialCreate
dotnet ef database update
```

### Step 5: Configure Services in Program.cs
- DbContext with SQL Server
- Session management
- HttpContextAccessor
- Stripe Service

### Step 6: Implement Authentication Flow
1. User registers → Creates User record → Creates Customer or HotelManager record
2. User logs in → Validates credentials → Sets session variables
3. Session variables: UserId, UserEmail, UserType, UserName

### Step 7: Implement Booking Flow
1. Customer searches hotels → `HotelsController.Index()`
2. Customer views hotel details → `HotelsController.Details()`
3. Customer creates booking → `BookingsController.Create()`
4. System validates availability and creates booking with "Pending" status
5. Redirects to payment page → `PaymentsController.Create()`

### Step 8: Implement Payment Flow
1. Payment page loads → Creates Stripe Payment Intent
2. Customer enters card details (handled by Stripe.js on frontend)
3. Frontend calls `PaymentsController.ConfirmPayment()`
4. Backend verifies payment with Stripe
5. Updates Payment and Booking status to "Succeeded" and "Confirmed"
6. Redirects to success page

### Step 9: Implement Hotel Manager Dashboard
1. Manager logs in → Redirected to `DashboardController.Index()`
2. Dashboard shows statistics and recent bookings
3. Manager can view all bookings → `DashboardController.Bookings()`
4. Manager can view all rooms → `DashboardController.Rooms()`
5. Manager can update booking status → `DashboardController.UpdateBookingStatus()`

### Step 10: Security Considerations
- Password hashing (SHA256 - consider upgrading to bcrypt/Argon2 in production)
- Session-based authentication
- Authorization checks in controllers (verify user type and ownership)
- SQL injection protection (Entity Framework parameterized queries)
- XSS protection (ASP.NET Core built-in)
- CSRF protection (ValidateAntiForgeryToken)

---

## Database Relationships Summary

```
User (1) ──< (1) Customer
User (1) ──< (1) HotelManager
HotelManager (N) ──> (1) Hotel
Hotel (1) ──< (N) Room
Customer (1) ──< (N) Booking
Hotel (1) ──< (N) Booking
Room (1) ──< (N) Booking
Booking (1) ──< (1) Payment
```

---

## Important Notes

1. **Password Security**: The current implementation uses SHA256. For production, use a more secure hashing algorithm like bcrypt or Argon2.

2. **Session Management**: Sessions are used for authentication. Consider implementing JWT tokens or ASP.NET Core Identity for production.

3. **Stripe Integration**: Make sure to use test keys during development and switch to live keys in production.

4. **Database Migrations**: Always create migrations for schema changes and test them before applying to production.

5. **Error Handling**: Add comprehensive error handling and logging in production.

6. **Hotel Manager Assignment**: Currently, Hotel Managers need to be manually assigned to hotels. Consider creating an admin interface for this.

---

## Next Steps

1. Create Views (Razor pages) for all controller actions
2. Implement frontend with Bootstrap and JavaScript
3. Add Stripe.js for payment form
4. Add validation and error handling
5. Add email notifications
6. Add admin panel for managing hotels and assigning managers
7. Implement booking cancellation with refunds
8. Add reviews and ratings system

