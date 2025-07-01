# API Endpoints Documentation

## Overview

This document outlines the planned API endpoints for the SaaS Grocery Platform. These endpoints will facilitate communication between the frontend applications (Customer Storefront, Vendor Admin Panel, Super Admin Panel) and the backend Laravel application. The API will follow RESTful principles where applicable.

## Key Endpoints

This section will detail the specific API endpoints required for different functionalities, categorized by the module they serve.

*   **Vendor Onboarding:** Endpoints for vendor registration, subdomain/domain setup, and initial store configuration.
    *   `POST /api/v1/vendor/register`: Register a new vendor and initiate the tenant provisioning process.
*   **Tenant Admin Panel:** Endpoints for managing products, orders, customers, store settings, shipping, and payments within a specific tenant's store.
    *   `GET /api/v1/tenant/products`: Retrieve a list of products for the authenticated vendor's store.
    *   `POST /api/v1/tenant/products`: Create a new product.
    *   `GET /api/v1/tenant/products/{id}`: Retrieve details of a specific product.
    *   `PUT /api/v1/tenant/products/{id}`: Update an existing product.
    *   `DELETE /api/v1/tenant/products/{id}`: Delete a product.
    *   `GET /api/v1/tenant/categories`: Retrieve a list of product categories.
    *   `GET /api/v1/tenant/orders`: Retrieve a list of orders for the authenticated vendor's store.
    *   `GET /api/v1/tenant/orders/{id}`: Retrieve details of a specific order.
    *   `PUT /api/v1/tenant/orders/{id}/status`: Update the status of an order.
    *   `PUT /api/v1/tenant/settings`: Update store settings.
    *   `PUT /api/v1/tenant/shipping`: Update shipping settings.
    *   `PUT /api/v1/tenant/payments`: Update payment gateway credentials.
*   **Customer Storefront:** Endpoints for browsing products, managing the shopping cart, checkout process, and customer account management.
    *   `GET /api/v1/store/{tenant}/products`: Retrieve a list of products for a specific tenant's store. The `{tenant}` parameter could be the tenant's subdomain or domain.
    *   `GET /api/v1/store/{tenant}/products/{id}`: Retrieve details of a specific product for a tenant.
    *   `POST /api/v1/store/{tenant}/cart/add`: Add a product to the shopping cart for a tenant's store.
    *   `PUT /api/v1/store/{tenant}/cart/update/{item_id}`: Update the quantity of an item in the cart.
    *   `DELETE /api/v1/store/{tenant}/cart/remove/{item_id}`: Remove an item from the cart.
    *   `POST /api/v1/store/{tenant}/checkout`: Initiate the checkout process for a tenant's store.
    *   `GET /api/v1/store/{tenant}/customer/orders`: Retrieve order history for a registered customer.
*   **Payments:** Endpoints specifically for handling payment processing and gateway interactions.
    *   `POST /api/v1/payments/process`: Process a payment for a given order. This might be called from the customer storefront or admin panel.
    *   `POST /api/v1/payments/webhook/{gateway}`: Endpoint for receiving webhooks from various payment gateways (e.g., `/api/v1/payments/webhook/stripe`, `/api/v1/payments/webhook/razorpay`).
*   **Subscriptions:** Endpoints for managing vendor subscriptions to the platform.
    *   `POST /api/v1/subscriptions/subscribe`: Subscribe a vendor to a specific plan.
    *   `POST /api/v1/subscriptions/cancel`: Cancel a vendor's subscription.
    *   `POST /api/v1/subscriptions/resume`: Resume a cancelled subscription.
    *   `PUT /api/v1/subscriptions/swap`: Change a vendor's subscription plan.
    *   `GET /api/v1/subscriptions/current`: Get the current subscription details for a vendor.
*   **Custom Domains:** Endpoints related to managing custom domain mapping for vendor stores.
*   **Analytics:** Endpoints for retrieving analytics data for both vendors and the super admin.
*   **Email/Notifications:** Endpoints potentially used for triggering transactional emails or notifications.

## Authentication/Authorization Considerations

This section will describe how API access will be secured.

*   **Authentication:** How users (Customers, Vendor Admins, Super Admins) will authenticate with the API (e.g., using tokens, sessions). Different authentication methods may be used for different user roles and types of access.
    *   Consider using Laravel Passport or Sanctum for API token based authentication for vendor and super admin panels.
    *   For the customer storefront API, session-based authentication (if using Laravel's built-in auth) or potentially a separate token system might be used.
*   **Authorization:** How the API will ensure that authenticated users only have access to the resources and actions they are permitted to perform based on their role and tenant affiliation. This is particularly crucial for multi-tenancy, ensuring vendors can only access data for their own store and super admins have appropriate platform-level access.
    *   Implement Laravel middleware to protect routes and check user permissions.
    *   For tenant-specific endpoints, ensure that the authenticated vendor user is associated with the tenant whose data is being accessed.