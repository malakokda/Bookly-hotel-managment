# Step-by-Step Implementation Checklist

## Phase 1: Database Setup ✅

- [x] Create all database models (User, Customer, HotelManager, Hotel, Room, Booking, Payment)
- [x] Configure DbContext with relationships
- [x] Add Entity Framework Core packages
- [ ] **TODO**: Create initial migration
  ```bash
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```
- [ ] **TODO**: Update connection string in `appsettings.json` for your SQL Server

---

## Phase 2: Configuration ✅

- [x] Update `WebApplication1.csproj` with NuGet packages
- [x] Configure services in `Program.cs`:
  - [x] DbContext
  - [x] Session management
  - [x] HttpContextAccessor
  - [x] Stripe Service
- [x] Update `appsettings.json` with:
  - [x] Connection string template
  - [x] Stripe keys template
- [ ] **TODO**: Get Stripe API keys from https://dashboard.stripe.com/test/apikeys
- [ ] **TODO**: Update `appsettings.json` with your actual Stripe keys

---

## Phase 3: Controllers ✅

- [x] AccountController (Register, Login, Logout, Profile)
- [x] HotelsController (Index, Details, Search)
- [x] BookingsController (Create, MyBookings, Cancel)
- [x] PaymentsController (Create, ConfirmPayment, Success)
- [x] DashboardController (Index, Bookings, Rooms, UpdateBookingStatus)

---

## Phase 4: Services ✅

- [x] IStripeService interface
- [x] StripeService implementation

---

## Phase 5: Frontend Implementation (Next Steps)

### Views to Create:

#### Account Views
- [ ] `Views/Account/Register.cshtml` - Registration form
- [ ] `Views/Account/Login.cshtml` - Login form
- [ ] `Views/Account/Profile.cshtml` - User profile with booking history

#### Hotels Views
- [ ] `Views/Hotels/Index.cshtml` - Hotel listing with search
- [ ] `Views/Hotels/Details.cshtml` - Hotel details with available rooms
- [ ] `Views/Hotels/Search.cshtml` - Search form

#### Bookings Views
- [ ] `Views/Bookings/Create.cshtml` - Booking confirmation page
- [ ] `Views/Bookings/MyBookings.cshtml` - Customer's booking list

#### Payments Views
- [ ] `Views/Payments/Create.cshtml` - Payment page with Stripe Elements
- [ ] `Views/Payments/Success.cshtml` - Payment success page

#### Dashboard Views
- [ ] `Views/Dashboard/Index.cshtml` - Manager dashboard with statistics
- [ ] `Views/Dashboard/Bookings.cshtml` - Manager's booking list
- [ ] `Views/Dashboard/Rooms.cshtml` - Manager's room list

---

## Phase 6: Stripe Integration (Frontend)

- [ ] Add Stripe.js script to payment page
- [ ] Create Stripe Elements form
- [ ] Handle payment submission
- [ ] Call `ConfirmPayment` endpoint after successful payment

**Example Stripe.js Integration**:
```html
<script src="https://js.stripe.com/v3/"></script>
<script>
  const stripe = Stripe('@ViewBag.StripePublishableKey');
  const elements = stripe.elements();
  // ... implement payment form
</script>
```

---

## Phase 7: Testing

### Test Scenarios:

#### Customer Flow
- [ ] Register as Customer
- [ ] Login as Customer
- [ ] Search for hotels
- [ ] View hotel details
- [ ] Create a booking
- [ ] Complete payment
- [ ] View booking history
- [ ] Cancel a booking

#### Hotel Manager Flow
- [ ] Register as Hotel Manager
- [ ] Login as Hotel Manager
- [ ] View dashboard
- [ ] View hotel bookings
- [ ] View hotel rooms
- [ ] Update booking status

#### Edge Cases
- [ ] Try to book unavailable room
- [ ] Try to access dashboard as customer
- [ ] Try to access bookings as non-owner
- [ ] Test payment failure scenarios

---

## Phase 8: Database Seeding (Optional)

Create a seed method to add initial data:

- [ ] Create sample hotels
- [ ] Create sample rooms
- [ ] Create test users (customers and managers)
- [ ] Assign managers to hotels

---

## Phase 9: Security Enhancements

- [ ] Implement proper password hashing (bcrypt/Argon2)
- [ ] Add input validation
- [ ] Add CSRF protection (already added with ValidateAntiForgeryToken)
- [ ] Add authorization attributes
- [ ] Add rate limiting
- [ ] Add logging

---

## Phase 10: Additional Features (Future)

- [ ] Email notifications
- [ ] Booking cancellation with refunds
- [ ] Reviews and ratings
- [ ] Admin panel for hotel management
- [ ] Image upload for hotels and rooms
- [ ] Advanced search filters
- [ ] Booking calendar view
- [ ] Reports and analytics

---

## Quick Start Commands

```bash
# Restore packages
dotnet restore

# Create database migration
dotnet ef migrations add InitialCreate

# Update database
dotnet ef database update

# Run the application
dotnet run
```

---

## Important Notes

1. **Database**: Make sure SQL Server is running before creating migrations
2. **Stripe**: Use test keys during development
3. **Session**: Sessions are stored in memory (use Redis for production)
4. **Password**: Current implementation uses SHA256 (upgrade for production)
5. **Connection String**: Update for your SQL Server instance

---

## Troubleshooting

### Migration Issues
- Ensure SQL Server is running
- Check connection string
- Delete `Migrations` folder and recreate if needed

### Stripe Issues
- Verify API keys are correct
- Check Stripe dashboard for logs
- Ensure payment intent is created correctly

### Session Issues
- Clear browser cookies
- Check session configuration in `Program.cs`
- Verify session middleware is added before routing

