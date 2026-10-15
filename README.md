# Cargo Management System

## Project Overview

The Cargo Management System (CMS) is a full-stack, microservices-based Logistics and Transportation Management Platform built to digitize and streamline end-to-end cargo operations — from customer onboarding and shipment booking to fleet assignment, warehouse handling, billing, and customer notifications.

The system is designed around an independently deployable microservices architecture rather than a traditional monolith, allowing each business capability (Auth, Customers, Shipments, Fleet, Warehouse, Billing, Dashboard, Notifications) to be developed, scaled, and maintained on its own. The project demonstrates a complete real-world software engineering workflow — covering requirements analysis (SRS-driven development), system design, backend service architecture, frontend development, event-driven communication, and iterative feature delivery.

The objective of the project is to give logistics businesses a centralized platform to manage shipments, fleet operations, warehouse inventory, customer billing, and real-time coordination between departments, replacing fragmented manual processes with a unified digital system.

## Objectives

The primary objectives of this project are:

- Design and implement a scalable microservices architecture for logistics operations.
- Manage the complete shipment lifecycle from booking to delivery.
- Coordinate fleet assignment and driver/vehicle management for shipments.
- Track warehouse operations including cargo receipts, bin management, and inventory.
- Automate billing operations including invoicing, payments, and rate card management.
- Enable asynchronous, event-driven communication between services for reliability and decoupling.
- Provide a centralized dashboard for operational visibility and reporting.
- Build a responsive, role-based frontend for staff to manage day-to-day logistics operations.

## Problem Statement

Logistics and cargo management involves coordinating multiple interdependent operations — customer requests, shipment scheduling, fleet availability, warehouse capacity, and billing — that are often handled through disconnected spreadsheets, manual communication, or siloed tools.

This fragmentation leads to delayed shipment assignments, inconsistent rate application, poor visibility into warehouse and fleet status, and billing errors.

This project addresses these challenges by developing a unified, service-oriented platform where shipment, fleet, warehouse, and billing operations are digitally connected and coordinated through defined APIs and asynchronous events, reducing manual handoffs and improving operational visibility.

## System Architecture

The system follows a **microservices architecture** with **Clean Architecture principles** applied within services, an **API Gateway** for unified client access, and an **event-driven backbone** for asynchronous cross-service communication.

### Microservices

| Service | Responsibility |
|---|---|
| **Auth Service** | User authentication, authorization, and identity management |
| **Customer Service** | Customer records, credit terms, and profile management |
| **Shipment Service** | Shipment booking, tracking, and lifecycle management (built with CQRS/MediatR) |
| **Fleet Service** | Vehicle and driver management, shipment-to-fleet assignment |
| **Warehouse Service** | Warehouse, bin, and cargo receipt management |
| **Billing Service** | Invoicing, payments, and rate card management |
| **Dashboard Service** | Cross-service reporting and operational analytics aggregation |
| **Notification Service** | Event-driven notification processing and email delivery |
| **API Gateway** | Single entry point routing client requests to backend services (built with Ocelot) |

### Architectural Highlights

- **Clean Architecture with CQRS/MediatR** in the Shipment service, separating command and query responsibilities for maintainability and testability.
- **Event-driven communication** using RabbitMQ, with publishers in the Billing, Fleet, Shipment, and Warehouse services and a consumer in the Notification service, enabling asynchronous cross-service workflows.
- **Saga pattern with compensating transactions** — the `AssignShipmentSaga` in the Fleet service coordinates multi-step shipment assignment and rolls back safely on failure.
- **Real-time communication infrastructure** — a SignalR `TrackingHub` in the Fleet service is scaffolded to support live vehicle and shipment tracking channels.
- **Per-service databases** — each microservice owns its own SQL Server database, following the database-per-service pattern for service independence.
- **API Gateway routing** — all client traffic is routed through a single Ocelot-based gateway, centralizing request routing across all nine services.

## Core Modules

### Shipment Management
Handles shipment creation, tracking, and status updates, forming the central workflow that other services (Fleet, Billing, Notification) react to via events.

### Fleet Management
Manages vehicles, drivers, and shipment assignments. Includes the `AssignShipmentSaga`, which coordinates assignment across services with automatic compensation if a step fails.

### Warehouse Management
Manages warehouses, storage bins, and cargo receipts — supporting inbound cargo processing and inventory organization.

### Billing Management
Handles invoice generation, payment recording, and **rate card management** — including creation, editing, and deactivation of shipment pricing rules used to calculate charges.

### Customer Management
Maintains customer profiles and credit terms used across billing and shipment workflows.

### Dashboard & Reporting
Aggregates data across services to provide operational reports covering shipments, fleet, warehouse, and revenue.

### Notifications
Consumes events published by other services (via RabbitMQ) and delivers notifications through email (SMTP).

## Frontend

The frontend is built with **Angular 21** using standalone components and Angular signals, structured around a feature-based architecture:

- **Auth** — Login and registration
- **Customers** — Customer records and credit terms management
- **Shipments** — Shipment creation, detail view, and tracking
- **Fleet** — Vehicles, drivers, and shipment assignments (tabbed interface)
- **Warehouse** — Warehouses, storage bins, and cargo receipts (tabbed interface)
- **Billing** — Invoices, payments, and rate cards (tabbed interface)
- **Reports** — Revenue, fleet, shipment, and warehouse reports with chart visualizations
- **Dashboard** — Operational summary view
- **Users** — User management

## Technical Stack

**Backend**
- .NET 10
- ASP.NET Core Web API
- Entity Framework Core
- SQL Server (per-service databases)
- MediatR (CQRS)
- RabbitMQ (event-driven messaging)
- Ocelot (API Gateway)
- SignalR (real-time communication infrastructure)

**Frontend**
- Angular 21 (standalone components, signals)
- TypeScript
- HTML5 / CSS3

**Architecture & Patterns**
- Microservices Architecture
- Clean Architecture
- CQRS with MediatR
- Event-Driven Architecture
- Saga Pattern with Compensating Transactions
- Database-per-Service
- API Gateway Pattern

## Project Structure

```
Cargo-Management-System/
│
├── CargoManagementSystem.Frontend/
│   └── src/app/
│       ├── core/          # Guards, interceptors, services, models
│       ├── features/      # Auth, Billing, Customers, Dashboard, Fleet, Reports, Shipments, Users, Warehouse
│       ├── layouts/
│       └── shared/
│
├── CargoManagementSystem.Microservices/
│   ├── ApiGateway/
│   ├── Auth.API / Auth.Application / Auth.Domain / Auth.Infrastructure
│   ├── Customer.API / Customer.Application / Customer.Domain / Customer.Infrastructure
│   ├── Shipment.API / Shipment.Application / Shipment.Domain / Shipment.Infrastructure
│   ├── Fleet.API / Fleet.Application / Fleet.Domain / Fleet.Infrastructure
│   ├── Warehouse.API / Warehouse.Application / Warehouse.Domain / Warehouse.Infrastructure
│   ├── Billing.API / Billing.Application / Billing.Domain / Billing.Infrastructure
│   ├── Dashboard.API / Dashboard.Application / Dashboard.Infrastructure
│   ├── Notification.API / Notification.Application / Notification.Domain / Notification.Infrastructure
│   ├── Shared.Contracts/   # Shared DTOs, Events, Exceptions
│   └── Tests/              # Unit and service-level tests
│
└── README.md
```

## Current Status

The system currently implements the core operational workflows across all nine services — shipment booking, fleet assignment, warehouse handling, and billing (including full Rate Card management) — with an event-driven backbone connecting them.

Areas identified for future development:

- **Document Management** — PDF generation for invoices, Bills of Lading (BOL), and Proof of Delivery (POD)
- **Payment Gateway Integration** — connecting the Billing service to a live payment processor
- **Notification Coverage** — extending event publishing to the Auth service and adding SMS delivery alongside email
- **Real-Time Tracking (Frontend)** — connecting the existing SignalR `TrackingHub` to live UI tracking views
- **Caching Layer** — introducing Redis/distributed caching for performance optimization
- **Containerization** — Docker-based deployment for all services

## Future Improvements

- Complete Document Management module with PDF generation and file storage.
- Integrate a payment gateway (e.g., Razorpay/Stripe) into the Billing service.
- Extend the Notification service to support SMS and complete event coverage across all services.
- Build a live fleet-tracking map view in the frontend consuming the existing SignalR hub.
- Introduce Redis caching and API rate limiting at the gateway level.
- Containerize all services with Docker and orchestrate with Docker Compose/Kubernetes.
- Add CI/CD pipelines for automated build, test, and deployment.

## Key Learnings

This project provided hands-on experience in several core areas of full-stack and distributed systems engineering:

- Designing and structuring a microservices architecture from an SRS specification.
- Applying Clean Architecture and CQRS patterns in a real backend service.
- Implementing event-driven communication using a message broker (RabbitMQ).
- Coordinating multi-step distributed workflows using the Saga pattern.
- Building a feature-based Angular frontend with standalone components and signals.
- Working across the full stack — from database design (SQL Server, EF Core) to API design to UI implementation.
- Managing a large multi-service codebase and understanding service boundaries and responsibilities.

## Conclusion

The Cargo Management System demonstrates the practical application of microservices architecture, event-driven design, and full-stack development to solve a real-world logistics coordination problem. Through a modular service design, asynchronous communication backbone, and a feature-rich Angular frontend, the project provides a solid foundation for a production-grade cargo and fleet management platform, while highlighting ongoing opportunities for expansion into document management, payments, and real-time tracking.
