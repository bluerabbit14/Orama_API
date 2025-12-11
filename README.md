# Orama API

Orama API is a comprehensive RESTful backend service built with **ASP.NET Core (.NET 9)** for user management, authentication, and admin operations. It provides secure user registration, login, password management, email verification with OTP via EmailJS, JWT-based authentication, and comprehensive admin controls.

## 🚀 Features

### User Management
- **User Registration** - Secure user signup with email validation
- **User Authentication** - JWT-based login system
- **Password Management** - Change password functionality
- **Profile Management** - Update user profile, phone number
- **Profile Retrieval** - Get authenticated user's profile
- **Account Deletion** - Users can delete their own accounts

### Email Services
- **Email Validation** - Validate email format
- **Email Registration Check** - Verify if email is already registered
- **Email Verification Status** - Check if email is verified
- **OTP Generation & Sending** - Generate and send OTP via EmailJS
- **OTP Verification** - Verify OTP codes with expiry validation
- **OTP Resend** - Resend OTP with rate limiting (max 2 active OTPs)
- **OTP Debug** - Debug endpoint for OTP troubleshooting

### Admin Features
- **Admin Registration & Login** - Separate admin authentication
- **User Management** - View all users, admins, search by ID/email/phone
- **User Status Control** - Activate/deactivate user accounts
- **User Profile Updates** - Admin can update any user's profile
- **User Deletion** - Admin can delete user accounts

### Security
- **JWT Authentication** - Secure token-based authentication
- **Role-Based Authorization** - User and Admin roles
- **Token Validation** - JWT token validation and debugging endpoints
- **OTP Security** - Time-limited OTPs (5 minutes expiry)
- **Rate Limiting** - Prevents OTP spam (max 2 active OTPs per email)

## 🛠️ Tech Stack

- **.NET 9.0** - Latest .NET framework
- **ASP.NET Core Web API** - RESTful API framework
- **Entity Framework Core 9.0.6** - ORM for database operations
- **SQL Server** - Database
- **JWT Bearer Authentication** - Token-based authentication
- **EmailJS** - Email service integration
- **Swagger/OpenAPI** - API documentation
- **DotNetEnv** - Environment variable management

## 📁 Project Structure

```
Orama_API/
├── Controllers/          # API controllers
│   ├── UserController.cs
│   ├── AdminController.cs
│   ├── Email_ServiceController.cs
│   └── Jwt_ServiceController.cs
├── Data/                 # Database context
│   └── UserDbContext.cs
├── Domain/               # Entity models
│   ├── UserProfile.cs
│   └── OTPEntity.cs
├── DTO/                  # Data Transfer Objects
│   ├── SignUpRequestDTO.cs
│   ├── LoginRequestDTO.cs
│   ├── EmailOTPRequestDTO.cs
│   └── ...
├── Interfaces/           # Service interfaces
│   ├── IUserService.cs
│   ├── IAdminService.cs
│   ├── IEmailService.cs
│   └── IJwtService.cs
├── Services/             # Business logic
│   ├── UserService.cs
│   ├── AdminService.cs
│   ├── EmailService.cs
│   └── JwtService.cs
├── Migrations/           # EF Core migrations
├── Program.cs            # Application entry point
├── appsettings.json      # Configuration
└── .env                  # Environment variables (not in repo)
```

## 📋 Prerequisites

- **.NET 9.0 SDK** - [Download](https://dotnet.microsoft.com/download/dotnet/9.0)
- **SQL Server** - SQL Server 2019 or later (or SQL Server Express)
- **EmailJS Account** - For email service (free tier available)
- **Visual Studio 2022** or **VS Code** (recommended)

## ⚙️ Setup Instructions

### 1. Clone the Repository

```bash
git clone <repository-url>
cd Orama_API
```

### 2. Configure Database Connection

Update the connection string in `appsettings.json`:

```json
{
  "ConnectionStrings": {
    "OramaMAUIConnection": "Server=YOUR_SERVER\\INSTANCE;Database=OramaAPI;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

Or set it via environment variable:
```bash
$env:ORAMA_CONNECTION_STRING="Server=YOUR_SERVER\\INSTANCE;Database=OramaAPI;Trusted_Connection=True;TrustServerCertificate=True;"
```

### 3. Configure EmailJS

Create a `.env` file in the project root (this file is gitignored):

```env
EMAILJS_SERVICE_ID=your_service_id
EMAILJS_TEMPLATE_ID=your_template_id
EMAILJS_PUBLIC_KEY=your_public_key
```

**EmailJS Template Requirements:**
Your EmailJS template should use these variables:
- `{{email}}` - Recipient email address
- `{{passcode}}` - The 6-digit OTP code
- `{{time}}` - OTP expiry time (format: HH:mm:ss)

**Example EmailJS Template:**
```
Subject: OTP for your [Company Name] authentication

To authenticate, please use the following One Time Password (OTP):
**{{passcode}}**
This OTP will be valid for 5 minutes till **{{time}}**.
```

### 4. Configure JWT Settings

Update JWT settings in `appsettings.json`:

```json
{
  "Jwt": {
    "Key": "YourSuperSecretKeyForJWTTokenGenerationYourSuperSecretKeyForJWTTokenGeneration",
    "Issuer": "OramaAPI",
    "Audience": "OramaUsers",
    "Subject": "MySubject",
    "TokenValidityMins": 60
  }
}
```

### 5. Run Database Migrations

```bash
# Create database and apply migrations
dotnet ef database update --context UserDbContext
```

If you need to create a new migration:
```bash
dotnet ef migrations add MigrationName --context UserDbContext
```

### 6. Run the Application

```bash
# Restore packages
dotnet restore

# Build the project
dotnet build

# Run the application
dotnet run
```

The API will be available at:
- **HTTP**: `http://localhost:5000` or `http://localhost:5112`
- **HTTPS**: `https://localhost:5001` or `https://localhost:7112`
- **Swagger UI**: `http://localhost:5112` (root URL in development)

## 🔐 Authentication

### JWT Token Structure

The API uses JWT tokens with the following claims:
- `UserId` - User ID
- `UserEmail` - User's email address
- `role` - User role ("User" or "Admin")
- Standard JWT claims (iss, aud, exp, etc.)

### Using JWT Tokens

Include the token in the Authorization header:
```
Authorization: Bearer <your_jwt_token>
```

### Token Validation Endpoints

- `POST /api/Jwt_Service/ValidateAndDebugToken` - Validate and debug JWT token
- `POST /api/Jwt_Service/ValidateTokenClaims` - Decode and view token claims

## 📡 API Endpoints

### User Endpoints

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/api/User/Register` | Register a new user | No |
| POST | `/api/User/Authorize` | User login | No |
| POST | `/api/User/ChangePassword` | Change password | No |
| GET | `/api/User/MyProfile` | Get my profile | Yes (User) |
| PATCH | `/api/User/UpdateProfile` | Update my profile | Yes (User) |
| POST | `/api/User/UpdatePhoneNumber` | Update phone number | Yes (User) |
| DELETE | `/api/User/DeleteMyProfile` | Delete my account | Yes (User) |

### Admin Endpoints

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/api/Admin/Register` | Register a new admin | No |
| POST | `/api/Admin/Authorize` | Admin login | No |
| GET | `/api/Admin/GetAllUser` | Get all users | Yes (Admin) |
| GET | `/api/Admin/GetAllAdmin` | Get all admins | Yes (Admin) |
| GET | `/api/Admin/GetById/{id}` | Get user by ID | Yes (Admin) |
| GET | `/api/Admin/GetByEmail?email={email}` | Get user by email | Yes (Admin) |
| GET | `/api/Admin/GetByPhone?phone={phone}` | Get user by phone | Yes (Admin) |
| PUT | `/api/Admin/AlterUserStatus/{id}` | Toggle user status | Yes (Admin) |
| PATCH | `/api/Admin/UpdateUserProfile/{id}` | Update user profile | Yes (Admin) |
| DELETE | `/api/Admin/DeleteUser/{id}` | Delete user | Yes (Admin) |

### Email Service Endpoints

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/api/Email_Service/IsEmailValid` | Validate email format | No |
| POST | `/api/Email_Service/IsEmailRegistered` | Check if email is registered | No |
| GET | `/api/Email_Service/IsEmailVerify/{Email}` | Check email verification status | No |
| POST | `/api/Email_Service/SendEmailOTP` | Send OTP email | No |
| POST | `/api/Email_Service/VerifyEmailOTP` | Verify OTP code | No |
| POST | `/api/Email_Service/ResendEmailOTP` | Resend OTP email | No |
| POST | `/api/Email_Service/DebugOTP` | Debug OTP information | No |

### JWT Service Endpoints

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/api/Jwt_Service/ValidateAndDebugToken` | Validate and debug token | No |
| POST | `/api/Jwt_Service/ValidateTokenClaims` | Decode token claims | No |

## 📧 Email Service Configuration

### EmailJS Setup

1. **Create EmailJS Account**: Sign up at [EmailJS](https://www.emailjs.com/)

2. **Create Email Service**: 
   - Go to Email Services
   - Add a new service (Gmail, Outlook, etc.)
   - Note your Service ID

3. **Create Email Template**:
   - Go to Email Templates
   - Create a new template
   - Use these variables:
     - `{{email}}` - Recipient email
     - `{{passcode}}` - OTP code
     - `{{time}}` - Expiry time
   - Note your Template ID

4. **Get Public Key**:
   - Go to Account → API Keys
   - Copy your Public Key

5. **Add to .env file**:
   ```env
   EMAILJS_SERVICE_ID=service_xxxxx
   EMAILJS_TEMPLATE_ID=template_xxxxx
   EMAILJS_PUBLIC_KEY=your_public_key
   ```

### OTP Features

- **OTP Format**: 6-digit numeric code
- **OTP Expiry**: 5 minutes
- **Rate Limiting**: Maximum 2 active (non-expired, non-used) OTPs per email
- **OTP Storage**: Stored in database with expiry tracking
- **Auto Cleanup**: Expired OTPs are automatically invalidated

## 🗄️ Database Schema

### UserProfile Table

| Column | Type | Description |
|--------|------|-------------|
| UserId | int | Primary key, auto-increment |
| Email | string(255) | Required, unique |
| Name | string(255) | Required |
| Password | string(20) | Required |
| Phone | string(20) | Optional |
| Address | string(255) | Optional |
| Role | string(20) | Default: "user" |
| IsEmailVerified | bool | Default: false |
| IsPhoneVerified | bool | Default: false |
| IsActive | bool | Default: true |
| CreatedAt | DateTime | Auto-set |
| LastUpdated | DateTime | Auto-updated |
| LastLogin | DateTime | Auto-set |
| ... | ... | Additional profile fields |

### OTPs Table

| Column | Type | Description |
|--------|------|-------------|
| Id | int | Primary key, auto-increment |
| Email | string(255) | Required, indexed |
| OTP | string(10) | Required, 6-digit code |
| CreatedAt | DateTime | OTP creation time |
| ExpiresAt | DateTime | OTP expiry time, indexed |
| IsUsed | bool | Default: false |
| UsedAt | DateTime? | When OTP was used |
| Purpose | string(50) | Default: "EmailVerification" |

## 🔧 Configuration Files

### appsettings.json

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "Jwt": {
    "Key": "YourSecretKey",
    "Issuer": "OramaAPI",
    "Audience": "OramaUsers",
    "TokenValidityMins": 60
  },
  "ConnectionStrings": {
    "OramaMAUIConnection": "YourConnectionString"
  },
  "EmailJSSettings": {
    "ServiceId": "optional_fallback",
    "TemplateId": "optional_fallback",
    "PublicKey": "optional_fallback"
  }
}
```

### .env File (Recommended)

```env
# Database Connection (optional, can use appsettings.json)
ORAMA_CONNECTION_STRING=Server=YOUR_SERVER;Database=OramaAPI;Trusted_Connection=True;

# EmailJS Configuration (required)
EMAILJS_SERVICE_ID=your_service_id
EMAILJS_TEMPLATE_ID=your_template_id
EMAILJS_PUBLIC_KEY=your_public_key
```

## 🧪 Testing

### Using Swagger UI

1. Run the application
2. Navigate to the root URL (e.g., `http://localhost:5112`)
3. Swagger UI will display all available endpoints
4. Use "Authorize" button to add JWT token for protected endpoints

### Example API Calls

**Register User:**
```bash
POST /api/User/Register
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "SecurePass123"
}
```

**Login:**
```bash
POST /api/User/Authorize
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "SecurePass123"
}
```

**Send OTP:**
```bash
POST /api/Email_Service/SendEmailOTP
Content-Type: application/json

{
  "email": "john@example.com"
}
```

## 🐛 Troubleshooting

### Database Connection Issues

- Verify SQL Server is running
- Check connection string format
- Ensure database exists or migrations will create it
- Verify SQL Server authentication settings

### EmailJS Issues

- Verify all three environment variables are set
- Check EmailJS template uses correct variable names: `{{email}}`, `{{passcode}}`, `{{time}}`
- Verify EmailJS service is active
- Check EmailJS account limits (free tier has limits)

### JWT Token Issues

- Verify JWT settings in appsettings.json
- Check token expiry time
- Use JWT debug endpoints to validate tokens
- Ensure "Bearer " prefix is included in Authorization header

### OTP Issues

- Use `/api/Email_Service/DebugOTP` endpoint to check OTP status
- Verify maximum 2 active OTPs limit
- Check OTP expiry time (5 minutes)
- Ensure EmailJS is properly configured

## 📝 Notes

- The `.env` file is gitignored for security
- JWT tokens expire after the configured time (default: 60 minutes)
- OTPs expire after 5 minutes
- Maximum 2 active OTPs per email address
- Swagger UI is only available in Development environment
- Database migrations are required before first run

## 📄 License

[Your License Here]

## 👥 Contributors

[Your Name/Team]

---

**For support or questions, please contact:** support@orama.com
