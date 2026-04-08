# Anypoint Platform Assessment Reports

This directory contains assessment reports and analysis for Anypoint Platform deployments, including hierarchy reports, runtime distribution analysis, and visualization charts.

## 🚀 Features

- **Full CRUD Operations** for Users and Products
- **Input Validation** using Joi schema validation
- **Pagination & Filtering** with customizable query parameters
- **Sorting** by multiple fields with ascending/descending order
- **Error Handling** with consistent error response format
- **Statistics Endpoints** for data analytics
- **Stock Management** for products with add/subtract/set operations
- **Security** with Helmet for HTTP headers
- **CORS** support for cross-origin requests
- **Logging** with Morgan middleware
- **Environment Configuration** with dotenv
- **API Documentation** with OpenAPI 3.0 and RAML specifications

## 📋 Table of Contents

- [Installation](#installation)
- [Getting Started](#getting-started)
- [API Endpoints](#api-endpoints)
- [Request/Response Examples](#requestresponse-examples)
- [API Specifications](#api-specifications)
- [Environment Variables](#environment-variables)
- [Project Structure](#project-structure)
- [Testing](#testing)
- [Contributing](#contributing)
- [License](#license)

## 🔧 Installation

1. **Clone or download the project files**

2. **Navigate to the project directory**
   ```bash
   cd output
   ```

3. **Install dependencies**
   ```bash
   npm install
   ```

4. **Create environment file (optional)**
   ```bash
   cp .env.example .env
   ```

5. **Start the server**
   ```bash
   # Production mode
   npm start
   
   # Development mode (with nodemon)
   npm run dev
   ```

6. **Verify installation**
   ```bash
   curl http://localhost:3000/health
   ```

## 🎯 Getting Started

### Quick Start

1. Start the server:
   ```bash
   npm start
   ```

2. The API will be available at `http://localhost:3000`

3. Check the health endpoint:
   ```bash
   curl http://localhost:3000/health
   ```

4. Explore the API documentation at `http://localhost:3000/`

### Sample Requests

**Create a user:**
```bash
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Alice Johnson",
    "email": "alice@example.com",
    "age": 28,
    "role": "developer"
  }'
```

**Get all users:**
```bash
curl http://localhost:3000/api/users
```

**Create a product:**
```bash
curl -X POST http://localhost:3000/api/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Smartphone X",
    "description": "Latest smartphone with advanced features",
    "price": 899.99,
    "category": "electronics",
    "stock": 100,
    "sku": "SP-001",
    "tags": ["smartphone", "mobile", "electronics"]
  }'
```

## 📚 API Endpoints

### Health Check
- `GET /health` - API health status

### Users
- `GET /api/users` - Get all users (with pagination & filtering)
- `POST /api/users` - Create a new user
- `GET /api/users/{id}` - Get user by ID
- `PUT /api/users/{id}` - Update user (full update)
- `PATCH /api/users/{id}` - Update user (partial update)
- `DELETE /api/users/{id}` - Delete user
- `GET /api/users/stats/summary` - Get user statistics

### Products
- `GET /api/products` - Get all products (with pagination & filtering)
- `POST /api/products` - Create a new product
- `GET /api/products/{id}` - Get product by ID
- `GET /api/products/sku/{sku}` - Get product by SKU
- `PUT /api/products/{id}` - Update product (full update)
- `PATCH /api/products/{id}` - Update product (partial update)
- `DELETE /api/products/{id}` - Delete product
- `PATCH /api/products/{id}/stock` - Update product stock
- `GET /api/products/stats/summary` - Get product statistics

## 📝 Request/Response Examples

### User Operations

#### Create User
```http
POST /api/users
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john.doe@example.com",
  "age": 30,
  "role": "developer"
}
```

**Response (201 Created):**
```json
{
  "message": "User created successfully",
  "data": {
    "id": "123e4567-e89b-12d3-a456-426614174000",
    "name": "John Doe",
    "email": "john.doe@example.com",
    "age": 30,
    "role": "developer",
    "createdAt": "2024-01-01T00:00:00.000Z",
    "updatedAt": "2024-01-01T00:00:00.000Z"
  }
}
```

#### Get Users with Filtering
```http
GET /api/users?role=developer&page=1&limit=5&sortBy=name&order=asc
```

**Response (200 OK):**
```json
{
  "data": [
    {
      "id": "1",
      "name": "Alice Johnson",
      "email": "alice@example.com",
      "age": 28,
      "role": "developer",
      "createdAt": "2024-01-01T00:00:00.000Z",
      "updatedAt": "2024-01-01T00:00:00.000Z"
    }
  ],
  "pagination": {
    "currentPage": 1,
    "totalPages": 1,
    "totalItems": 1,
    "itemsPerPage": 5,
    "hasNextPage": false,
    "hasPrevPage": false
  },
  "filters": {
    "role": "developer",
    "name": null,
    "sortBy": "name",
    "order": "asc"
  }
}
```

### Product Operations

#### Create Product
```http
POST /api/products
Content-Type: application/json

{
  "name": "Laptop Pro",
  "description": "High-performance laptop for professionals",
  "price": 1299.99,
  "category": "electronics",
  "stock": 50,
  "sku": "LP-001",
  "tags": ["laptop", "computer", "professional"],
  "isActive": true
}
```

#### Update Product Stock
```http
PATCH /api/products/{id}/stock
Content-Type: application/json

{
  "quantity": 25,
  "operation": "add"
}
```

**Response (200 OK):**
```json
{
  "message": "Stock updated successfully",
  "data": {
    "id": "product-id",
    "previousStock": 50,
    "currentStock": 75,
    "operation": "add",
    "quantity": 25
  }
}
```

### Error Responses

All errors follow a consistent format:

```json
{
  "error": "Validation Error",
  "message": "\"email\" must be a valid email",
  "field": "email",
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

Common HTTP status codes:
- `400` - Validation Error
- `404` - Resource Not Found
- `409` - Conflict (duplicate resource)
- `500` - Internal Server Error

## 📖 API Specifications

The API is fully documented using industry standards:

### OpenAPI 3.0 Specification
- **File**: `api-spec/openapi.yaml`
- **View**: Import the OpenAPI spec into tools like Swagger UI, Postman, or Insomnia
- **Features**: Complete schema definitions, examples, and response codes

### RAML 1.0 Specification
- **File**: `api-spec/api.raml`
- **View**: Use MuleSoft's API Designer or other RAML tools
- **Features**: Comprehensive type definitions, traits, and documentation

### Usage Examples

**Swagger UI:**
```bash
# Install swagger-ui-serve globally
npm install -g swagger-ui-serve

# Serve the OpenAPI spec
swagger-ui-serve api-spec/openapi.yaml
```

**API Console (for RAML):**
```bash
# Install api-console-cli globally
npm install -g api-console-cli

# Serve the RAML spec
api-console serve api-spec/api.raml
```

## ⚙️ Environment Variables

Copy `.env.example` to `.env` and customize as needed:

```bash
# Server Configuration
NODE_ENV=development
PORT=3000
HOST=localhost

# API Configuration
API_VERSION=v1
API_PREFIX=/api

# Security Configuration
JWT_SECRET=your-super-secret-jwt-key-change-this-in-production
JWT_EXPIRES_IN=24h

# Rate Limiting
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100

# CORS Configuration
CORS_ORIGIN=*
CORS_METHODS=GET,HEAD,PUT,PATCH,POST,DELETE

# Logging Configuration
LOG_LEVEL=info
LOG_FORMAT=combined

# Development Tools
ENABLE_SWAGGER=true
ENABLE_DEBUG_LOGS=true
```

## 📁 Project Structure

```
output/
├── package.json          # Project dependencies and scripts
├── server.js            # Main server file
├── .env.example         # Environment variables template
├── README.md           # This documentation
├── routes/             # API route handlers
│   ├── users.js        # User CRUD operations
│   └── products.js     # Product CRUD operations
├── api-spec/           # API specifications
│   ├── openapi.yaml    # OpenAPI 3.0 specification
│   └── api.raml       # RAML 1.0 specification
└── tests/             # Test files (optional)
    ├── unit/          # Unit tests
    └── integration/   # Integration tests
```

### Key Files

- **`server.js`**: Main application entry point with Express setup, middleware configuration, and route mounting
- **`routes/users.js`**: Complete user management with CRUD operations, validation, and statistics
- **`routes/products.js`**: Product management including stock operations and SKU lookup
- **`api-spec/`**: Complete API documentation in both OpenAPI and RAML formats

## 🧪 Testing

### Running Tests

```bash
# Run all tests
npm test

# Run unit tests only
npm run test:unit

# Run integration tests only
npm run test:integration
```

### Manual Testing

**Test Users API:**
```bash
# Get all users
curl http://localhost:3000/api/users

# Create a user
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Test User","email":"test@example.com","age":25,"role":"user"}'

# Get user statistics
curl http://localhost:3000/api/users/stats/summary
```

**Test Products API:**
```bash
# Get all products
curl http://localhost:3000/api/products

# Filter products by category
curl "http://localhost:3000/api/products?category=electronics&inStock=true"

# Create a product
curl -X POST http://localhost:3000/api/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Test Product","description":"A test product for demonstration","price":99.99,"category":"other","stock":10,"sku":"TEST-001","tags":["test"]}'
```

### Validation Testing

Test input validation:
```bash
# Invalid email (should return 400)
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Test","email":"invalid-email","age":25,"role":"user"}'

# Invalid SKU format (should return 400)
curl -X POST http://localhost:3000/api/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Test","description":"Test product","price":10,"category":"other","stock":5,"sku":"invalid-sku","tags":["test"]}'
```

## 🔧 Advanced Features

### Pagination

All list endpoints support pagination:
```bash
# Get page 2 with 5 items per page
curl "http://localhost:3000/api/users?page=2&limit=5"
```

### Filtering

**Users:**
- `role`: Filter by user role
- `name`: Partial name matching

**Products:**
- `category`: Filter by product category
- `name`: Partial name matching
- `minPrice` / `maxPrice`: Price range filtering
- `inStock`: Filter by stock availability
- `isActive`: Filter by active status
- `tags`: Filter by tags (comma-separated)

### Sorting

Both users and products support sorting:
```bash
# Sort users by age (ascending)
curl "http://localhost:3000/api/users?sortBy=age&order=asc"

# Sort products by price (descending)
curl "http://localhost:3000/api/products?sortBy=price&order=desc"
```

### Statistics

Get analytical data:
```bash
# User statistics
curl http://localhost:3000/api/users/stats/summary

# Product statistics
curl http://localhost:3000/api/products/stats/summary
```

## 🔒 Security Considerations

This example API includes basic security measures:

- **Helmet**: Sets various HTTP headers for security
- **CORS**: Configurable cross-origin resource sharing
- **Input Validation**: Joi schema validation for all inputs
- **Error Handling**: Prevents sensitive information leakage

**For Production:**
1. Add authentication (JWT recommended)
2. Implement rate limiting
3. Add request/response logging
4. Use HTTPS
5. Validate and sanitize all inputs
6. Add database integration with proper connection pooling
7. Implement proper error monitoring

## 📈 Performance Considerations

- **Pagination**: All list endpoints are paginated to prevent large responses
- **Filtering**: Database-level filtering should be implemented for better performance
- **Caching**: Consider adding Redis for caching frequent requests
- **Database**: Replace in-memory storage with a proper database (PostgreSQL, MongoDB)
- **Monitoring**: Add performance monitoring and logging

## 🤝 Contributing

1. Fork the project
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🔗 Additional Resources

- [Express.js Documentation](https://expressjs.com/)
- [Joi Validation](https://joi.dev/)
- [OpenAPI Specification](https://swagger.io/specification/)
- [RAML Specification](https://raml.org/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)

---

**Need Help?** Open an issue or contact the development team.
