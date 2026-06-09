# MassLab EShop 🛒

Welcome to **masslab.eshop**, a simplified yet highly scalable and production-ready E-Commerce platform. This project serves as a reference implementation of a distributed microservices architecture built on top of the **MassLab-Net** ecosystem.

The entire system is designed using **Clean Architecture**, leveraging our internal core libraries for foundational blocks, identity management, and content management.

---

## 🏛 System Architecture Overview

The system uses a combination of **REST/GraphQL** for public/admin facing clients and **gRPC** for high-performance internal service-to-service communication. Asynchronous workflows and decoupling are handled via a **Message Broker**.

### Architecture Diagram (Mermaid)

```mermaid
graph TD
    %% Clients
    UserWeb[Public B2C Web / App]
    AdminPortal[Admin Management Portal]

    %% Gateway / Auth
    Gateway[API Gateway / YARP]
    Identity[masslab.identity.api]
    CMS[masslab.cms.api]

    %% eShop Core Services
    ProductSvc[Product Service]
    CartSvc[Cart Service]
    OrderSvc[Order Service]
    PromoSvc[Promotion Service]
    PaymentSvc[Payment Service]

    %% Infrastructure
    MsgBroker[Message Broker / MassTransit]
    CommonLib[[masslab.common.*]]

    %% Connections
    UserWeb -->|REST / GraphQL| Gateway
    AdminPortal -->|REST / GraphQL| Gateway
    
    Gateway --> Identity
    Gateway --> CMS
    Gateway --> ProductSvc
    Gateway --> CartSvc
    Gateway --> OrderSvc
    Gateway --> PromoSvc
    Gateway --> PaymentSvc

    %% Internal Communication
    OrderSvc -.->|gRPC Internal| ProductSvc
    OrderSvc -.->|gRPC Internal| PromoSvc
    CartSvc -.->|gRPC Internal| ProductSvc

    %% Event Driven
    OrderSvc ===|Publish Events| MsgBroker
    MsgBroker ===|Subscribe Events| PaymentSvc
    MsgBroker ===|Subscribe Events| PromoSvc

    %% Global Dependency
    CommonLib -.->|Injected Into| ProductSvc
    CommonLib -.->|Injected Into| OrderSvc
    CommonLib -.->|Injected Into| CartSvc
    CommonLib -.->|Injected Into| PromoSvc
    CommonLib -.->|Injected Into| PaymentSvc
