# Uge8 - Multi-Service Docker Application

This project consists of multiple microservices running in Docker containers with PostgreSQL database and PostgREST API.

## Architecture

The application consists of the following services:

- **ProductService** (ASP.NET Core) - Manages products, runs on port 7125
- **OrderService** (ASP.NET Core) - Manages orders, runs on port 5028
- **PostgreSQL Database** - Data storage, runs on port 5432
- **PostgREST** - REST API for direct database access, runs on port 3000

## Prerequisites

- Docker and Docker Compose installed
- .NET 9.0 SDK (for local development)
- Git

## Quick Start

### Running with Docker Compose

1. **Clone the repository:**
   ```bash
   git clone --recurse-submodules <repository-url>
   cd uge8
   ```
   
   *Or if you already cloned without submodules:*
   ```bash
   git clone <repository-url>
   cd uge8
   git submodule update --init --recursive
   ```

2. **Start all services (production mode):**
   ```bash
   docker-compose -f docker-compose.yml -f uge6_DBService/db/docker-compose.yml up -d
   ```

3. **Start all services (debug mode with VS Code debugging support):**
   ```bash
   docker-compose -f docker-compose.yml -f compose.debug.yaml -f uge6_DBService/db/docker-compose.yml up -d
   ```

4. **Stop all services:**
   ```bash
   docker-compose -f docker-compose.yml -f compose.debug.yaml -f uge6_DBService/db/docker-compose.yml down
   ```

5. **Stop all services and remove volumes (fresh database):**
   ```bash
   docker-compose -f docker-compose.yml -f compose.debug.yaml -f uge6_DBService/db/docker-compose.yml down -v
   ```

## Service URLs

Once running, the services are available at:

- **ProductService API**: http://localhost:7125
- **ProductService Swagger**: http://localhost:7125/swagger
- **OrderService API**: http://localhost:5028
- **OrderService Swagger**: http://localhost:5028/swagger
- **PostgREST API**: http://localhost:3000
- **PostgreSQL Database**: localhost:5432

## Database

### Connection Details
- **Host**: localhost (from host machine) or `db` (from containers)
- **Port**: 5432
- **Database**: appdb
- **Username**: app
- **Password**: secret

### Database Seeding
The database is automatically seeded with sample data from CSV files located in `uge6_DBService/db/init/seed/`:
- users.csv
- products.csv
- orders.csv
- order_items.csv

### Database Schema
The database includes the following tables:
- `users` - User accounts
- `products` - Product catalog
- `orders` - Customer orders
- `order_items` - Individual items in orders

## Development

### Project Structure
```
uge8/
├── docker-compose.yml              # Main services configuration
├── compose.debug.yaml              # Debug configuration with VS Code support
├── uge6_ProductService/            # Product service
│   └── ProductService/
│       ├── ProductService.sln
│       ├── ProductService/         # Main API project
│       └── ProductService.Test/    # Unit tests
├── uge8_OrderDocker/              # Order service
│   ├── OrderService.sln
│   ├── OrderService/              # Main API project
│   └── OrderService.test/         # Unit tests
└── uge6_DBService/                # Database configuration
    └── db/
        ├── docker-compose.yml     # Database services
        └── init/                  # Database initialization scripts
            ├── *.sql              # Schema and setup scripts
            └── seed/              # CSV data files
```

### Building Individual Services

**ProductService:**
```bash
cd uge6_ProductService/ProductService
dotnet build
dotnet run --project ProductService
```

**OrderService:**
```bash
cd uge8_OrderDocker
dotnet build
dotnet run --project OrderService
```

### Running Tests

**ProductService Tests:**
```bash
cd uge6_ProductService/ProductService
dotnet test
```

**OrderService Tests:**
```bash
cd uge8_OrderDocker
dotnet test
```

## Docker Configuration

### Networks
All services run on a shared Docker network (`app-network`) allowing inter-service communication using service names.

### Debug Mode
Debug mode includes:
- Debug build configuration
- VS Code remote debugging support (vsdbg volume mount)
- Development environment settings

### Environment Variables
Services use environment variables to override configuration:
- `ASPNETCORE_ENVIRONMENT=Development`
- `ConnectionStrings__DefaultConnection` - Database connection string

## API Documentation

### ProductService Endpoints
- `GET /api/Products` - Get all products
- `GET /api/Products/{id}` - Get product by ID
- `POST /api/Products` - Create new product
- `PUT /api/Products/{id}` - Update product
- `DELETE /api/Products/{id}` - Delete product

### OrderService Endpoints
- `GET /api/Orders` - Get all orders
- `GET /api/Orders/{id}` - Get order by ID
- `POST /api/Orders` - Create new order
- `PUT /api/Orders/{id}` - Update order
- `DELETE /api/Orders/{id}` - Delete order

### PostgREST API
PostgREST provides a REST API directly to the PostgreSQL database:
- `GET /products` - Get products
- `GET /orders` - Get orders
- `GET /users` - Get users
- `GET /order_items` - Get order items

## Troubleshooting

### Common Issues

1. **Database not seeded**: 
   - Remove volumes and restart: `docker-compose down -v && docker-compose up -d`

2. **Service can't connect to database**:
   - Ensure database is healthy before services start
   - Check connection strings use service name `db` not `localhost`

3. **Port conflicts**:
   - Check if ports 3000, 5028, 5432, 7125 are available on your system
   - Modify port mappings in docker-compose files if needed

4. **Services can't communicate**:
   - Ensure all services are on the same network (`app-network`)
   - Use service names (e.g., `productservice`, `orderservice`) for inter-service calls

### Logs
View logs for specific services:
```bash
docker logs productservice
docker logs orderservice
docker logs uge8-db-1
docker logs uge8-postgrest-1
```

### Rebuilding Services
To rebuild a specific service after code changes:
```bash
docker-compose -f docker-compose.yml -f compose.debug.yaml -f uge6_DBService/db/docker-compose.yml up -d --build productservice
```

## Contributing

1. Make changes to the code
2. Test locally with `dotnet test`
3. Rebuild Docker images with `--build` flag
4. Verify all services work together

## License

[Add your license information here]