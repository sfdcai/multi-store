# Database Schema Documentation

This document outlines the database schema for the SaaS Grocery Platform. It is divided into sections for global tables (platform-level) and tenant-specific tables.

## Global Tables

These tables are shared across the entire platform and manage global data like tenants and platform users.

### `tenants`

Stores information about each individual grocery store tenant.

- `id`: VARCHAR (255) - Unique identifier for the tenant (likely UUID or similar). Primary Key.
- `name`: VARCHAR (255) - The name of the tenant's store. Required.
- `domain`: VARCHAR (255) - The subdomain or custom domain associated with the tenant. Unique.
- `database`: VARCHAR (255) - The name of the tenant's dedicated database (if using database separation). Required.
- `created_at`: TIMESTAMP - Timestamp when the tenant was created.
- `updated_at`: TIMESTAMP - Timestamp when the tenant information was last updated.

### `users`

Stores information about platform-level users, such as the super admin. Tenant-specific users (vendor admins and customers) will be stored in the tenant databases.

- `id`: BIGINT (20) - Unique identifier for the user. Primary Key, Auto-increment.
- `name`: VARCHAR (255) - The user's name. Required.
- `email`: VARCHAR (255) - The user's email address. Unique. Required.
- `email_verified_at`: TIMESTAMP - Timestamp when the email was verified.
- `password`: VARCHAR (255) - The user's hashed password. Required.
- `remember_token`: VARCHAR (100) - Token for "remember me" functionality.
- `created_at`: TIMESTAMP - Timestamp when the user was created.
- `updated_at`: TIMESTAMP - Timestamp when the user information was last updated.

### `subscriptions`

Stores information about vendor subscriptions to the platform.

- `id`: BIGINT (20) - Unique identifier for the subscription. Primary Key, Auto-increment.
- `tenant_id`: VARCHAR (255) - Foreign Key referencing the `tenants` table. Required.
- `stripe_id`: VARCHAR (255) - The Stripe subscription ID. Required.
- `stripe_status`: VARCHAR (255) - The current status of the Stripe subscription. Required.
- `stripe_plan`: VARCHAR (255) - The Stripe plan ID associated with the subscription. Required.
- `quantity`: INT (11) - The quantity of the subscription (if applicable).
- `trial_ends_at`: TIMESTAMP - Timestamp when the trial period ends.
- `ends_at`: TIMESTAMP - Timestamp when the subscription ends (if cancelled).
- `created_at`: TIMESTAMP - Timestamp when the subscription was created.
- `updated_at`: TIMESTAMP - Timestamp when the subscription was last updated.

## Tenant-Specific Tables

These tables reside within each tenant's individual database and store data specific to that grocery store.

### `products`

Stores product information for the tenant's store.

- `id`: BIGINT (20) - Unique identifier for the product. Primary Key, Auto-increment.
- `name`: VARCHAR (255) - The name of the product. Required.
- `description`: TEXT - A detailed description of the product.
- `price`: DECIMAL (8, 2) - The price of the product. Required.
- `category_id`: BIGINT (20) - Foreign Key referencing the `categories` table. Nullable.
- `image_url`: VARCHAR (255) - The URL to the product image.
- `stock`: INT (11) - The current stock quantity of the product.
- `is_published`: BOOLEAN - Indicates if the product is visible on the storefront. Default: true.
- `created_at`: TIMESTAMP - Timestamp when the product was created.
- `total_views`: INTEGER - The total number of times the product has been viewed. Default: 0.
- `total_sales`: INTEGER - The total number of times the product has been sold. Default: 0.
- `updated_at`: TIMESTAMP - Timestamp when the product information was last updated.

### `categories`

Stores product categories for the tenant's store.

- `id`: BIGINT (20) - Unique identifier for the category. Primary Key, Auto-increment.
- `name`: VARCHAR (255) - The name of the category. Unique. Required.
- `slug`: VARCHAR (255) - A URL-friendly slug for the category. Unique. Required.
- `created_at`: TIMESTAMP - Timestamp when the category was created.
- `updated_at`: TIMESTAMP - Timestamp when the category information was last updated.

### `customers`

Stores customer information for customers who have created accounts on the tenant's store.

- `id`: BIGINT (20) - Unique identifier for the customer. Primary Key, Auto-increment.
- `name`: VARCHAR (255) - The customer's name. Required.
- `email`: VARCHAR (255) - The customer's email address. Unique within the tenant. Required.
- `password`: VARCHAR (255) - The customer's hashed password. Required.
- `created_at`: TIMESTAMP - Timestamp when the customer was created.
- `updated_at`: TIMESTAMP - Timestamp when the customer information was last updated.

### `orders`

Stores order information for the tenant's store.

- `id`: BIGINT (20) - Unique identifier for the order. Primary Key, Auto-increment.
- `customer_id`: BIGINT (20) - Foreign Key referencing the `customers` table. Nullable (for guest orders).
- `order_number`: VARCHAR (255) - A unique identifier for the order (e.g., generated sequence). Required.
- `order_date`: TIMESTAMP - The date and time the order was placed. Required.
- `total_amount`: DECIMAL (10, 2) - The total amount of the order. Required.
- `payment_status`: VARCHAR (50) - The current payment status (e.g., pending, paid, failed). Required.
- `shipping_status`: VARCHAR (50) - The current shipping status (e.g., pending, shipped, delivered).
- `shipping_address_id`: BIGINT (20) - Foreign Key referencing the `shipping_addresses` table. Nullable.
- `billing_address_id`: BIGINT (20) - Foreign Key referencing the `shipping_addresses` table (if separate billing address). Nullable.
- `created_at`: TIMESTAMP - Timestamp when the order was created.
- `total_items`: INTEGER - The total number of individual items in the order.
- `discount_amount`: DECIMAL (10, 2) - The total discount applied to the order.
- `tax_amount`: DECIMAL (10, 2) - The total tax applied to the order.
- `updated_at`: TIMESTAMP - Timestamp when the order information was last updated.

### `order_items`

Stores individual items within an order for the tenant's store.

- `id`: BIGINT (20) - Unique identifier for the order item. Primary Key, Auto-increment.
- `order_id`: BIGINT (20) - Foreign Key referencing the `orders` table. Required.
- `product_id`: BIGINT (20) - Foreign Key referencing the `products` table. Required.
- `quantity`: INT (11) - The quantity of the product in the order item. Required.
- `price`: DECIMAL (8, 2) - The price of the product at the time of the order. Required.
- `created_at`: TIMESTAMP - Timestamp when the order item was created.
- `updated_at`: TIMESTAMP - Timestamp when the order item information was last updated.

### `payments`

Stores payment transaction details for orders in the tenant's store.

- `id`: BIGINT (20) - Unique identifier for the payment. Primary Key, Auto-increment.
- `order_id`: BIGINT (20) - Foreign Key referencing the `orders` table. Required.
- `transaction_id`: VARCHAR (255) - The transaction ID from the payment gateway. Unique. Required.
- `payment_method`: VARCHAR (50) - The method of payment (e.g., Stripe, Razorpay, Credit Card). Required.
- `amount`: DECIMAL (10, 2) - The amount of the payment. Required.
- `currency`: VARCHAR (10) - The currency of the payment. Required.
- `status`: VARCHAR (50) - The status of the payment (e.g., succeeded, failed, refunded). Required.
- `payload`: JSON - Stores additional data from the payment gateway. Nullable.
- `created_at`: TIMESTAMP - Timestamp when the payment record was created.
- `updated_at`: TIMESTAMP - Timestamp when the payment record was last updated.

### `shipping_addresses`

Stores shipping and potentially billing addresses for customers in the tenant's store.

- `id`: BIGINT (20) - Unique identifier for the address. Primary Key, Auto-increment.
- `customer_id`: BIGINT (20) - Foreign Key referencing the `customers` table. Nullable (for guest orders).
- `address_line_1`: VARCHAR (255) - The first line of the address. Required.
- `address_line_2`: VARCHAR (255) - The second line of the address. Nullable.
- `city`: VARCHAR (100) - The city. Required.
- `state`: VARCHAR (100) - The state or province. Required.
- `zip_code`: VARCHAR (50) - The zip or postal code. Required.
- `country`: VARCHAR (100) - The country. Required.
- `created_at`: TIMESTAMP - Timestamp when the address was created.
- `updated_at`: TIMESTAMP - Timestamp when the address information was last updated.

### `settings`

Stores tenant-specific settings and configurations for their store.

- `id`: BIGINT (20) - Unique identifier for the settings. Primary Key, Auto-increment.
- `store_name`: VARCHAR (255) - The name of the tenant's store. Required.
- `logo_url`: VARCHAR (255) - The URL to the store's logo. Nullable.
- `contact_email`: VARCHAR (255) - The contact email for the store. Nullable.
- `phone_number`: VARCHAR (50) - The contact phone number for the store. Nullable.
- `currency`: VARCHAR (10) - The default currency for the store. Required.
- `payment_gateway_config`: JSON - JSON object storing payment gateway API keys and settings. Nullable.
- `shipping_options`: JSON - JSON object storing shipping options and rates. Nullable.
- `created_at`: TIMESTAMP - Timestamp when the settings were created.
- `updated_at`: TIMESTAMP - Timestamp when the settings were last updated.
## Global Analytics Tables

These tables will store aggregated data from all tenants for platform-level analytics and reporting for the Super Admin.

### `platform_sales_summary`

Aggregates sales data across all tenants.

- `id`: Primary key.
- `date`: The date for which the data is summarized.
- `total_revenue`: Total revenue generated across all tenants for the given date.
- `total_orders`: Total number of orders placed across all tenants for the given date.
- `average_order_value`: The average value of orders across all tenants for the given date.
- `created_at`: Timestamp when the record was created.
- `updated_at`: Timestamp when the record was last updated.

### `tenant_performance_metrics`

Tracks key performance indicators for each tenant.

- `id`: Primary key.
- `tenant_id`: Foreign key referencing the `tenants` table.
- `date`: The date or reporting period for which the metrics are calculated.
- `total_sales`: Total sales generated by the tenant for the given period.
- `number_of_orders`: Total number of orders placed with the tenant for the given period.
- `new_customers`: Number of new customers acquired by the tenant during the given period.
- `created_at`: Timestamp when the record was created.
- `updated_at`: Timestamp when the record was last updated.
