# Stage 3: Security Improvements with Environment Variables

## Overview
This document explains the security improvements implemented in Stage 3 by moving sensitive configuration data from the docker-compose.yml file into separate .env files.

## Security Issues in Stage 2
In the original configuration, sensitive information was hardcoded directly in the docker-compose.yml file:
- Database passwords were visible in plain text
- Azure Storage account keys were exposed in the configuration
- All credentials were stored in version control
- No separation of concerns between different types of configuration

## Security Improvements Implemented

### 1. Environment File Separation
Created separate .env files for different concerns:
- `.env.product_db` - Product database credentials
- `.env.order_db` - Order database credentials  
- `.env.product_service` - Product service configuration including Azure Storage
- `.env.order_service` - Order service configuration

### 2. Credential Isolation
**Before (Stage 2):**
```yaml
environment:
  POSTGRES_PASSWORD: postgres
  AZURE_STORAGE_ACCOUNT_KEY: 90c6sZmwSkj4M27llOtupHvY5WzOCYbT3hTAryoBf7JvJgcaZ6EQTWaNOcyeq7WJ1w0hWqDvyNaT+ASt4qnbyA==
```

**After (Stage 3):**
```yaml
env_file:
  - .env.product_service
```

### 3. Version Control Protection
Added `.gitignore` file to prevent sensitive .env files from being committed:
```
.env.azure
.env.database
.env.product_service
.env.order_service
.env.product_db
.env.order_db
```

### 4. Example Files for Developers
Created `.example` files showing required variables without exposing actual values:
- `.env.example`
- `.env.azure.example`
- `.env.database.example`

## Security Benefits

1. **Credential Protection**: Sensitive data is no longer visible in configuration files
2. **Version Control Safety**: Credentials cannot be accidentally committed to repositories
3. **Environment Separation**: Different environments can use different credentials easily
4. **Principle of Least Privilege**: Each service only gets the environment variables it needs
5. **Audit Trail**: Changes to credentials don't pollute git history
6. **Team Security**: Developers can work with example files without accessing production credentials

## Container Architecture

### Services and Ports:
- **Frontend**: `localhost:3000` (Nginx serving static files)
- **Product Service**: `localhost:8000` (FastAPI with Azure Storage integration)
- **Order Service**: `localhost:8001` (FastAPI with product service communication)
- **Product Database**: `localhost:5432` (PostgreSQL)
- **Order Database**: `localhost:5433` (PostgreSQL)

### Service Dependencies:
- Order Service depends on Product Service and Order Database
- Product Service depends on Product Database
- Frontend depends on both API services
- All services use health checks for reliable startup

### Azure Storage Integration:
- Account Name: `sit722storage2`
- Container: `productimages`
- SAS Token Expiry: 24 hours
- Used for product image storage and retrieval

## Testing Results
After implementing the security improvements:
- ✅ All containers start successfully
- ✅ Database connections work with environment variables
- ✅ Azure Storage integration functions properly
- ✅ Frontend application loads and displays correctly
- ✅ API endpoints respond as expected
- ✅ No sensitive data exposed in configuration files

## Best Practices Implemented
1. **Separation of Concerns**: Different .env files for different services
2. **Documentation**: Example files guide developers
3. **Version Control Hygiene**: .gitignore prevents credential leaks
4. **Minimal Exposure**: Each service gets only necessary variables
5. **Easy Deployment**: Environment-specific configurations possible

This approach significantly improves the security posture of the application while maintaining functionality and ease of deployment.