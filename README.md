# 🛒 Talabat APIs — E-commerce Backend (ASP.NET Core)

A RESTful **e-commerce Web API** inspired by Talabat, built with **ASP.NET Core (.NET 6)** following **Onion / Clean Architecture**. It covers product browsing with filtering, sorting, search & pagination, JWT authentication, a Redis-backed shopping basket, and order management.

<p align="left">
  <img alt=".NET" src="https://img.shields.io/badge/.NET-6.0-512BD4?logo=dotnet&logoColor=white" />
  <img alt="ASP.NET Core Web API" src="https://img.shields.io/badge/ASP.NET%20Core-Web%20API-512BD4?logo=dotnet&logoColor=white" />
  <img alt="EF Core" src="https://img.shields.io/badge/EF%20Core-6.0-512BD4" />
  <img alt="SQL Server" src="https://img.shields.io/badge/SQL%20Server-CC2927?logo=microsoftsqlserver&logoColor=white" />
  <img alt="Redis" src="https://img.shields.io/badge/Redis-Basket-DC382D?logo=redis&logoColor=white" />
  <img alt="JWT" src="https://img.shields.io/badge/Auth-JWT-000000?logo=jsonwebtokens&logoColor=white" />
  <img alt="Swagger" src="https://img.shields.io/badge/Swagger-OpenAPI-85EA2D?logo=swagger&logoColor=black" />
</p>

> 🧠 **100% human-written.** This project was designed and coded entirely by hand — **no AI tools or code generators were used** in its development.

---

## 📑 Table of Contents

- [Features](#-features)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [API Endpoints](#-api-endpoints)
- [Getting Started](#-getting-started)
- [Configuration](#-configuration)
- [Project Structure](#-project-structure)
- [Author](#-author)

---

## ✨ Features

- 🛍️ **Products** — list with **filtering** (by brand & type), **sorting**, **search**, and **pagination**; fetch a single product, all brands, and all types.
- 🔐 **Authentication** — register / login issuing **JWT** tokens; get current user, check email existence, and manage the user's address.
- 🧺 **Shopping basket** — create, read, and delete baskets stored in **Redis** with a 30-day TTL.
- 📦 **Orders** — create an order from a basket, list the current user's orders, get an order by id, and list delivery methods.
- 🧩 **Specification pattern** — reusable, composable query logic for filtering, includes, sorting, and paging.
- 🗺️ **AutoMapper** — entity ↔ DTO mapping with custom resolvers (e.g. absolute picture URLs).
- 🧱 **Global error handling** — exception middleware plus standardized API response / validation error wrappers.
- 🌱 **Auto seeding** — products, brands, types, delivery methods, and test users seeded from JSON on startup.
- 📜 **Swagger / OpenAPI** — interactive API docs in development.

---

## 🏗️ Architecture

**Onion / Clean Architecture** with dependencies pointing inward toward the Core:

| Project | Layer | Responsibility |
|---------|-------|----------------|
| **Talabat.Core** | Domain | Entities, enums, repository & service interfaces, **specifications**, Unit of Work contract. |
| **Talabat.Repository** | Infrastructure | EF Core `StoreContext` & `AppIdentityDbContext`, generic repository, Redis basket repository, Unit of Work, specification evaluator, migrations & seeding. |
| **Talabat.Service** | Application | Business logic — `TokenService` (JWT) and `OrderService`. |
| **TalabatAPI** | Presentation | Controllers, DTOs, AutoMapper profiles, error types, middleware, DI extensions, `Program.cs`. |

Patterns used: **Generic Repository**, **Unit of Work**, **Specification**, and **DTO** mapping.

---

## 🧰 Tech Stack

| Area | Technology |
|------|-----------|
| Framework | ASP.NET Core Web API, **.NET 6.0** |
| ORM | Entity Framework Core 6.0 |
| Databases | SQL Server — `Talabat.APIs` (store) + `Talabat.APIsIdentityDb` (identity) |
| Caching | Redis (StackExchange.Redis) for baskets |
| Auth | ASP.NET Core Identity + JWT Bearer |
| Mapping | AutoMapper 12 |
| Docs | Swashbuckle (Swagger / OpenAPI) |

---

## 🔌 API Endpoints

### Products — `/api/products`
| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/products` | List products (`pageIndex`, `pageSize`, `sort`, `brandId`, `typeId`, `search`) |
| GET | `/api/products/{id}` | Get a product by id |
| GET | `/api/products/brands` | List all brands |
| GET | `/api/products/types` | List all types |

### Accounts — `/api/accounts`
| Method | Route | Description |
|--------|-------|-------------|
| POST | `/api/accounts/register` | Register, returns JWT |
| POST | `/api/accounts/login` | Login, returns JWT |
| GET | `/api/accounts` 🔒 | Get current user |
| GET / PUT | `/api/accounts/address` 🔒 | Get / update user address |
| GET | `/api/accounts/emailexists?email=` | Check if email is taken |

### Baskets — `/api/baskets`
| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/baskets?id=` | Get basket from Redis |
| POST | `/api/baskets` | Create / update basket |
| DELETE | `/api/baskets?id=` | Delete basket |

### Orders — `/api/orders` 🔒
| Method | Route | Description |
|--------|-------|-------------|
| POST | `/api/orders` | Create an order from a basket |
| GET | `/api/orders` | List current user's orders |
| GET | `/api/orders/{id}` | Get an order by id |
| GET | `/api/orders/deliverymethods` | List delivery methods |

🔒 = requires a Bearer token.

---

## 🚀 Getting Started

### Prerequisites

- [.NET SDK 6.0](https://dotnet.microsoft.com/download/dotnet/6.0)
- SQL Server (LocalDB, Express, or full)
- A running **Redis** instance (default `localhost:6379`)
- Visual Studio 2022 or VS Code with the C# extension

### Setup

```bash
# 1. Clone the repository
git clone https://github.com/AhmedAlaaElsaadani/Talapat-APIs.git
cd Talapat-APIs

# 2. (Optional) update connection strings in TalabatAPI/appsettings.json

# 3. Restore dependencies
dotnet restore

# 4. Run the API (migrations are applied & data is seeded automatically on startup)
dotnet run --project TalabatAPI
```

Then open **Swagger** at `https://localhost:5001/swagger`.

> To apply migrations manually:
> ```bash
> dotnet ef database update -p Talabat.Repository -s TalabatAPI --context StoreContext
> dotnet ef database update -p Talabat.Repository -s TalabatAPI --context AppIdentityDbContext
> ```

---

## ⚙️ Configuration

All settings live in `TalabatAPI/appsettings.json`:

- **ConnectionStrings** — `DefaultConnection` (store DB), `IdentityConnection` (identity DB), `Redis`.
- **JWT** — `Key`, `Issuer`, `Audience`, `DurationInDays`.
- **ApiBaseUrl** — base URL used to build absolute image URLs.

> ⚠️ For real deployments, move the JWT key and connection strings out of source control into environment variables / user-secrets, and use a strong signing key.

Seed data is loaded from `Talabat.Repository/Data/DataSeed/` (`brands.json`, `types.json`, `products.json`, `delivery.json`).

---

## 📂 Project Structure

```
Talapat-APIs/
├── TalabatAPI.sln
├── Talabat.Core/           # Entities, interfaces, specifications, Unit of Work contract
├── Talabat.Repository/     # EF Core contexts, generic & basket repos, evaluator, seeding, migrations
├── Talabat.Service/        # TokenService (JWT), OrderService
└── TalabatAPI/             # Controllers, DTOs, helpers, errors, middleware, extensions, Program.cs
```

---

## 👤 Author

**Ahmed Alaa Elsaadani**

- GitHub: [@AhmedAlaaElsaadani](https://github.com/AhmedAlaaElsaadani)

---

> ⭐ If you find this project helpful, consider giving it a star on GitHub!
