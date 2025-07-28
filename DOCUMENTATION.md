#  Documentation

## Software Architecture Diagram

┌───────────────────────────────────────────────────────────────────────────────┐
│                           PODMAN COMPOSE ARCHITECTURE                         │
├───────────────────────────────────────────────────────────────────────────────┤
│                                                                               │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐            │
│  │   Frontend      │    │  Product API    │    │   Order API     │            │
│  │                 │    │   (FastAPI)     │    │   (FastAPI)     │            │
│  │   Port: 3000    │    │   Port: 8000    │    │   Port: 8001    │            │
│  └─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘            │
│            │                      │                      │                    │
│            │ HTTP Requests        │ HTTP Requests        │ HTTP Requests      │
│            │                      │                      │                    │
│            └──────────────────────┼──────────────────────┘                    │
│                                   │                                           │
│                                   │                                           │
│            ┌─────────────────────┐│┌─────────────────────┐                    │
│            │   Product DB        │││   Order DB          │                    │
│            │   (PostgreSQL)      │││   (PostgreSQL)      │                    │ 
│            │   Port: 5432        │││   Port: 5433        │                    │ 
│            └─────────────────────┘│└─────────────────────┘                    │
│                                   │                                           │
│                                   │                                           │
│            ┌─────────────────────────────────────────────┐                    │
│            │         Azure Storage Account               │                    │
│            │   Account: sit722storage2                   │                    │
│            │   Container: product-images                 │                    │
│            │   SAS Token Expiry: 24 hours                │                    │
│            └─────────────────────────────────────────────┘                    │
│                                                                               │
└───────────────────────────────────────────────────────────────────────────────┘

### Container Details:

| Container Name | Image | Port Mapping | Environment Files |
|----------------|-------|--------------|-------------------|
| product_order_frontend_e2 | sit722registry2.azurecr.io/frontend:latest | 3000:80 | - |
| product_api_container_e2 | sit722registry2.azurecr.io/product_service:latest | 8000:8000 | .env.product_service |
| order_api_container_e2 | sit722registry2.azurecr.io/order_service:latest | 8001:8000 | .env.order_service |
| product_db_container_e2 | postgres:13 | 5432:5432 | .env.product_db |
| order_db_container_e2 | postgres:13 | 5433:5432 | .env.order_db |

### Network Links:
- Frontend → Product API (port 8000)
- Frontend → Order API (port 8001)
- Product API → Product DB (port 5432)
- Order API → Order DB (port 5433)
- Product API → Azure Storage (HTTPS)
- Order API → Product API (HTTP)

### Azure Storage Credentials:
- **Storage Account**: sit722storage2
- **Container Name**: product-images
- **Access Method**: SAS Token
- **Token Expiry**: 24 hours
- **Permissions**: Read, Write, List

