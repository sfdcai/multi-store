# Data Flow Diagrams

This document provides visual representations of the key data flows within the SaaS Grocery Platform.

## 1. Vendor Onboarding and Tenant Provisioning Flow

This diagram shows how a new vendor signs up and how a new tenant, database, and subdomain are provisioned.


+----------------+ +-------------------------+ +--------------------+ | Vendor's |----->| SaaS Platform |----->| Global Database | | Browser | | (Registration Form) | | (Create Tenant | +----------------+ +-------------------------+ | Record) | | +--------------------+ | (Tenant Provisioning Service) V +-------------------+ +-------------------------+ | Tenant Database |<-----| Run Tenant Migrations | | (Create DB) | +-------------------------+ +-------------------+ | V +-------------------+ | Web Server | | (Configure | | Subdomain) | +-------------------+ | V +-------------------+ | Email Service | | (Send Welcome | | Email) | +-------------------+

## 2. Customer Storefront Flow

This diagram illustrates how a customer interacts with a vendor's store, places an order, and how the data is stored in the tenant's database.


+----------------+ +-------------------------+ | Customer's |----->| Web Server (Nginx) | | Browser | | (e.g., freshmart.com) | +----------------+ +-------------------------+ | | (Request for a tenant's store) V +-------------------------+ +-------------------+ | Laravel Application |----->| Tenant Database | | (Tenant Middleware) | | (Fetch Products)| +-------------------------+ +-------------------+ | | (Customer places an order) V +-------------------------+ | Payment Gateway | | (Process Payment) | +-------------------------+ | | (Payment successful) V +-------------------------+ +-------------------+ | Laravel Application |----->| Tenant Database | | (Create Order, | | (Store Order, | | Payment, Customer) | | Payment, etc.) | +-------------------------+ +-------------------+

## 3. Tenant Admin Panel Flow

This diagram shows how a vendor manages their store, and how that data is stored and retrieved from their tenant database.


+----------------+ +-------------------------+ | Vendor's |----->| Web Server (Nginx) | | Browser | | (e.g., admin.myapp.com)| +----------------+ +-------------------------+ | | (Request for tenant admin panel) V +-------------------------+ +-------------------+ | Laravel Application |----->| Tenant Database | | (Tenant Middleware, | | (Fetch Data) | | Auth Middleware) | | | +-------------------------+ +-------------------+ | | (Vendor adds a new product) V +-------------------------+ +-------------------+ | Laravel Application |----->| Tenant Database | | (ProductController) | | (Create Product)| +-------------------------+ +-------------------+

## 4. Analytics Data Flow

This diagram illustrates how data from tenant databases is aggregated into the global analytics tables for the Super Admin.


+-------------------+ +-------------------------+ | Tenant Database |----->| Scheduled Job | | (Orders, etc.) | | (e.g., daily) | +-------------------+ +-------------------------+ | | (Aggregates data from all tenants) V +-------------------------+ | Global Analytics | | Tables | | (platform_sales_summary,| | tenant_performance) | +-------------------------+ | | V +-------------------------+ | Super Admin | | Dashboard | +-------------------------+