Low Level Design (LLD) for Parking Lot System

Technology Stack:

Backend: Python (FastAPI or Flask)

Frontend: React.js

Cache: Redis

Database: PostgreSQL

1. Entities and Data Models (PostgreSQL)

1.1 User

Field

Type

Description

id

UUID (PK)

Unique user ID

name

TEXT

Full name

email

TEXT

Unique email address

password_hash

TEXT

Hashed password

role

ENUM

[Admin, Attendant, Customer]

1.2 Vehicle

Field

Type

Description

id

UUID (PK)

Vehicle ID

license_plate

TEXT

Unique license plate

vehicle_type

ENUM

[Car, Bike, Truck]

user_id

UUID (FK)

Owner (customer)

1.3 ParkingSpot

Field

Type

Description

id

UUID (PK)

Spot ID

spot_type

ENUM

[Car, Bike, Truck]

floor

INT

Floor number

is_occupied

BOOLEAN

Whether the spot is in use

1.4 Ticket

Field

Type

Description

id

UUID (PK)

Ticket ID

vehicle_id

UUID (FK)

Associated vehicle

spot_id

UUID (FK)

Assigned parking spot

entry_time

TIMESTAMP

Entry timestamp

exit_time

TIMESTAMP

Exit timestamp (nullable)

status

ENUM

[Active, Closed]

amount

NUMERIC

Calculated parking fee (nullable)

1.5 Payment

Field

Type

Description

id

UUID (PK)

Payment ID

ticket_id

UUID (FK)

Related ticket

amount

NUMERIC

Amount paid

method

TEXT

Payment method (Cash/Card)

status

ENUM

[Paid, Failed]

timestamp

TIMESTAMP

Payment time

2. Redis Cache Design

available_spots:{vehicle_type}: Set of available spot IDs per vehicle type

ticket:{ticket_id}: Cached active ticket details

dashboard:active_tickets: Cached list of active tickets (periodic update)

user_session:{user_id}: Session/token data (if auth enabled)

3. Backend API Endpoints

3.1 Authentication

POST /login: User login → returns token

POST /register: Register new customer

3.2 Parking Actions

POST /entry: Vehicle entry → assigns spot, creates ticket

POST /exit: Vehicle exit → calculates price, updates ticket

GET /ticket/{id}: View ticket details

3.3 Admin / Attendant Functions

GET /dashboard: View all active tickets

GET /spots: View current spot availability

POST /spots: Add/update parking spots

GET /users: Admin access to list users (optional)

4. Frontend Components (React.js)

Pages

Login/Register Page: User authentication

Home Page: Vehicle entry/exit forms

Dashboard: Admin/attendant view of current active tickets

Availability Page: Live spot status

Pricing Info Page: View rates per vehicle type

Components

VehicleEntryForm

VehicleExitForm

SpotStatusTable

TicketSummary

DashboardTable

State Handling

Axios for API communication

React Context or Redux for session/ticket state

5. Business Logic

Pricing Strategy

Hourly rates:

Bike: $5/hr

Car: $10/hr

Truck: $20/hr

Grace period: 15 minutes (free)

Slot Assignment Logic

Check Redis set available_spots:{type}

Allocate first available spot

Update Redis & DB for occupancy

Exit Logic

Fetch ticket from Redis → fallback to DB

Calculate duration & price

Update ticket, free up spot

Store payment record

6. Security & Authentication

Use JWT tokens for login sessions

Bcrypt for password hashing

Role-based access control for Admin, Attendant, Customer

7. Future Enhancements

Real-time WebSocket updates for dashboards

QR code on ticket for scan-exit

Rate limit on entry/exit API

Audit logs for admin actions

This LLD provides a foundation for building your full-featured parking lot system with Python, Redis, PostgreSQL, and React.