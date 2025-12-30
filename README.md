# Clean Identity Template (.NET 8, Clean Architecture)

این ریپازیتوری یک تمپلیت آماده **Clean Architecture** با سیستم احراز هویت کامل است.

## 🏗 ساختار Solution

```
src/
└─ Identity/
   ├─ Identity.Domain          → موجودیت‌های دامنه (خالص، بدون وابستگی به فریمورک)
   ├─ Identity.Application     → Use cases، Commands، Queries، Handlers
   ├─ Identity.Infrastructure  → EF Core، DbContext، Password Hashing، JWT، Email Service
   └─ Identity.Api             → ASP.NET Core Web API (JWT-ready، Serilog، Swagger)
```

## ✨ ویژگی‌ها

- ✅ **Clean Architecture** با جداسازی کامل لایه‌ها
- ✅ **Entity Framework Core** با SQL Server
- ✅ **JWT Authentication** با Refresh Token
- ✅ **MediatR** برای CQRS Pattern
- ✅ **FluentValidation** برای اعتبارسنجی
- ✅ **Serilog** برای لاگینگ
- ✅ **Swagger/OpenAPI** با پشتیبانی از JWT
- ✅ **Exception Handling Middleware**
- ✅ **Password Hashing** با ASP.NET Identity PasswordHasher

## 📦 NuGet Packages

### Domain Layer
- هیچ وابستگی خارجی ندارد (Pure Domain)

### Application Layer
- MediatR (12.2.0)
- FluentValidation (11.9.0)
- FluentValidation.DependencyInjectionExtensions (11.9.0)

### Infrastructure Layer
- Microsoft.EntityFrameworkCore (8.0.0)
- Microsoft.EntityFrameworkCore.SqlServer (8.0.0)
- Microsoft.AspNetCore.Identity (2.2.0)
- Microsoft.IdentityModel.Tokens (7.0.3)
- System.IdentityModel.Tokens.Jwt (7.0.3)
- Serilog.AspNetCore (8.0.0)
- Serilog.Sinks.Console (5.0.1)
- Serilog.Sinks.File (5.0.0)

### API Layer
- Microsoft.AspNetCore.Authentication.JwtBearer (8.0.0)
- Swashbuckle.AspNetCore (6.5.0)
- Serilog.AspNetCore (8.0.0)

## 🔐 API Endpoints

### Authentication

#### 1. ثبت‌نام (Signup)
```http
POST /api/Auth/signup
Content-Type: application/json

{
  "userName": "testuser",
  "email": "test@example.com",
  "password": "P@ssw0rd123",
  "firstName": "Test",
  "lastName": "User"
}
```

#### 2. ورود (Login)
```http
POST /api/Auth/login
Content-Type: application/json

{
  "email": "test@example.com",
  "password": "P@ssw0rd123"
}
```

**Response:**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "base64-encoded-refresh-token",
  "expiresAt": "2024-01-01T12:00:00Z",
  "user": {
    "id": "guid",
    "userName": "testuser",
    "email": "test@example.com",
    "firstName": "Test",
    "lastName": "User"
  }
}
```

#### 3. فراموشی رمز عبور (Forget Password)
```http
POST /api/Auth/forget-password
Content-Type: application/json

{
  "email": "test@example.com"
}
```

#### 4. بازنشانی رمز عبور (Reset Password)
```http
POST /api/Auth/reset-password
Content-Type: application/json

{
  "token": "reset-token-from-email",
  "email": "test@example.com",
  "newPassword": "NewP@ssw0rd123"
}
```

#### 5. اطلاعات کاربر فعلی (Get Current User)
```http
GET /api/Auth/me
Authorization: Bearer {accessToken}
```

## 🚀 راه‌اندازی

### پیش‌نیازها

- .NET SDK 8.0 یا بالاتر
- SQL Server (Local یا Remote)

### مراحل نصب

1. **کلون کردن ریپازیتوری:**
   ```bash
   git clone https://github.com/Amiralisa5/clean-identity-template.git
   cd clean-identity-template
   ```

2. **تنظیم Connection String:**
   
   فایل `src/Identity/Identity.Api/appsettings.json` را ویرایش کنید:
   ```json
   {
     "ConnectionStrings": {
       "IdentityDb": "Server=.;Database=IdentityDb;Trusted_Connection=True;TrustServerCertificate=True;"
     }
   }
   ```

3. **تنظیم JWT Secret Key:**
   
   در فایل `appsettings.json`، یک Secret Key قوی برای JWT تنظیم کنید:
   ```json
   {
     "JwtSettings": {
       "SecretKey": "YourSuperSecretKeyThatShouldBeAtLeast32CharactersLong!",
       "Issuer": "IdentityApi",
       "Audience": "IdentityApi",
       "ExpirationMinutes": "60"
     }
   }
   ```

4. **Build و Run:**
   ```bash
   dotnet restore
   dotnet build
   dotnet run --project src/Identity/Identity.Api/Identity.Api.csproj
   ```

5. **دسترسی به Swagger UI:**
   
   مرورگر را باز کنید و به آدرس زیر بروید:
   - https://localhost:5001/swagger
   - یا http://localhost:5000/swagger

## 📝 نکات مهم

### Database Migration
پروژه به صورت خودکار Migration را اجرا می‌کند. اگر می‌خواهید به صورت دستی Migration ایجاد کنید:

```bash
cd src/Identity/Identity.Infrastructure
dotnet ef migrations add InitialCreate --startup-project ../Identity.Api/Identity.Api.csproj
dotnet ef database update --startup-project ../Identity.Api/Identity.Api.csproj
```

### Email Service
سرویس Email فعلاً فقط لاگ می‌کند. برای استفاده در Production، باید یک سرویس Email واقعی (مثل SendGrid، SMTP و ...) پیاده‌سازی کنید.

### Password Requirements
- حداقل 6 کاراکتر
- حداقل یک حرف بزرگ
- حداقل یک حرف کوچک
- حداقل یک عدد

## 🏛 معماری

### Domain Layer
- موجودیت‌های خالص دامنه (User، RefreshToken، PasswordResetToken)
- بدون وابستگی به فریمورک‌ها

### Application Layer
- Commands و Queries با MediatR
- DTOs برای انتقال داده
- Interfaces برای Abstraction
- FluentValidation برای اعتبارسنجی

### Infrastructure Layer
- پیاده‌سازی EF Core DbContext
- Password Hashing
- JWT Token Generation
- Email Service (Stub)

### API Layer
- Controllers
- Middleware برای Exception Handling
- JWT Authentication
- Swagger Configuration

## 📄 License

Apache License 2.0
