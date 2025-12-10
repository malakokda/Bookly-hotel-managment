# Controllers Quick Reference Guide

## Controller Overview

### 1. AccountController
**Route**: `/Account/*`
**Purpose**: User authentication and profile management

| Action | Method | Route | Description |
|--------|--------|-------|-------------|
| Register | GET/POST | `/Account/Register` | User registration (Customer or Hotel Manager) |
| Login | GET/POST | `/Account/Login` | User login |
| Logout | GET | `/Account/Logout` | User logout |
| Profile | GET | `/Account/Profile` | View user profile and booking history |

**Key Features**:
- Handles both Customer and Hotel Manager registration
- Session-based authentication
- Password hashing
- Redirects based on user type after login

---

### 2. HotelsController
**Route**: `/Hotels/*`
**Purpose**: Hotel search and viewing

| Action | Method | Route | Description |
|--------|--------|-------|-------------|
| Index | GET | `/Hotels` | List all hotels with optional filters |
| Details | GET | `/Hotels/Details/{id}` | View hotel details and available rooms |
| Search | GET/POST | `/Hotels/Search` | Hotel search form |

**Query Parameters for Index**:
- `City` - Filter by city
- `CheckInDate` - Check-in date
- `CheckOutDate` - Check-out date
- `NumberOfGuests` - Number of guests
- `MaxPrice` - Maximum price per night

**Query Parameters for Details**:
- `checkIn` - Check-in date
- `checkOut` - Check-out date
- `guests` - Number of guests

**Key Features**:
- Availability checking based on existing bookings
- Filters inactive hotels
- Shows only available rooms for selected dates

---

### 3. BookingsController
**Route**: `/Bookings/*`
**Purpose**: Booking creation and management

| Action | Method | Route | Description |
|--------|--------|-------|-------------|
| Create | GET/POST | `/Bookings/Create` | Create a new booking |
| MyBookings | GET | `/Bookings/MyBookings` | View all customer bookings |
| Cancel | POST | `/Bookings/Cancel/{id}` | Cancel a booking |

**Query Parameters for Create**:
- `roomId` - Room ID to book
- `checkIn` - Check-in date
- `checkOut` - Check-out date
- `guests` - Number of guests

**Key Features**:
- Validates room availability
- Calculates total amount automatically
- Creates booking with "Pending" status
- Redirects to payment after creation

---

### 4. PaymentsController
**Route**: `/Payments/*`
**Purpose**: Stripe payment processing

| Action | Method | Route | Description |
|--------|--------|-------|-------------|
| Create | GET | `/Payments/Create` | Display payment page |
| ConfirmPayment | POST | `/Payments/ConfirmPayment` | Confirm payment with Stripe |
| Success | GET | `/Payments/Success` | Payment success page |

**Query Parameters for Create**:
- `bookingId` - Booking ID to pay for

**Key Features**:
- Creates Stripe Payment Intent
- Stores payment information
- Updates booking status after payment
- Handles payment verification

---

### 5. DashboardController
**Route**: `/Dashboard/*`
**Purpose**: Hotel Manager dashboard (Hotel Managers only)

| Action | Method | Route | Description |
|--------|--------|-------|-------------|
| Index | GET | `/Dashboard` | Dashboard overview with statistics |
| Bookings | GET | `/Dashboard/Bookings` | View all hotel bookings |
| Rooms | GET | `/Dashboard/Rooms` | View all hotel rooms |
| UpdateBookingStatus | POST | `/Dashboard/UpdateBookingStatus` | Update booking status |

**Key Features**:
- Only accessible to Hotel Managers
- Shows statistics: total bookings, confirmed, pending, rooms
- Displays recent bookings
- Managers only see their assigned hotel's data

---

## User Flow Examples

### Customer Booking Flow
1. **Search Hotels**: `GET /Hotels/Search` → `POST /Hotels/Search` → `GET /Hotels?City=...`
2. **View Hotel**: `GET /Hotels/Details/1?checkIn=...&checkOut=...&guests=2`
3. **Create Booking**: `GET /Bookings/Create?roomId=1&checkIn=...&checkOut=...&guests=2` → `POST /Bookings/Create`
4. **Pay**: `GET /Payments/Create?bookingId=1` → `POST /Payments/ConfirmPayment` → `GET /Payments/Success?bookingId=1`
5. **View Bookings**: `GET /Bookings/MyBookings`

### Hotel Manager Flow
1. **Login**: `GET /Account/Login` → `POST /Account/Login` → Redirects to `/Dashboard`
2. **View Dashboard**: `GET /Dashboard` (shows statistics)
3. **View Bookings**: `GET /Dashboard/Bookings`
4. **Update Status**: `POST /Dashboard/UpdateBookingStatus?bookingId=1&status=Confirmed`
5. **View Rooms**: `GET /Dashboard/Rooms`

---

## Session Variables

The application uses the following session variables:
- `UserId` (int) - Current user's ID
- `UserEmail` (string) - Current user's email
- `UserType` (string) - "Customer" or "HotelManager"
- `UserName` (string) - User's full name

---

## Authorization Rules

1. **Public Access**: 
   - Hotel search and viewing
   - Registration and login pages

2. **Customer Only**:
   - Creating bookings
   - Viewing own bookings
   - Payment processing
   - Profile page

3. **Hotel Manager Only**:
   - Dashboard
   - Viewing hotel bookings
   - Viewing hotel rooms
   - Updating booking status

4. **Both Customer and Hotel Manager**:
   - Logout
   - Profile (with different data)

---

## Status Values

### Booking Status
- `Pending` - Created but payment not completed
- `Confirmed` - Payment completed, booking confirmed
- `Cancelled` - Booking cancelled
- `Completed` - Check-out completed

### Payment Status
- `Pending` - Payment initiated but not completed
- `Succeeded` - Payment successful
- `Failed` - Payment failed
- `Refunded` - Payment refunded

