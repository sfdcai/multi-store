# Database Schema

This document outlines the database schema for the SaaS Grocery Platform, including both global tables for platform management and tenant-specific tables for individual grocery stores.

## Global Tables

These tables reside in the central platform database.

### `tenants`

Stores information about each tenant (grocery store).

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `uuid`: `CHAR(36)` - Unique identifier for the tenant (UUID). `UNIQUE`, `INDEX`
- `name`: `VARCHAR(255)` - The name of the grocery store.
- `slug`: `VARCHAR(255)` - A URL-friendly slug for the tenant (e.g., for subdomains). `UNIQUE`, `INDEX`
- `domain`: `VARCHAR(255)` - The custom domain associated with the tenant (if any). `UNIQUE`, `NULLABLE`, `INDEX`
- `database`: `VARCHAR(255)` - The name of the tenant's database. `UNIQUE`
- `is_active`: `BOOLEAN` - Indicates if the tenant is active. `DEFAULT 1`, `INDEX`
- `created_at`: `TIMESTAMP` - Timestamp when the tenant was created.
- `updated_at`: `TIMESTAMP` - Timestamp when the tenant was last updated.

**Relationships:**

- One `tenant` has many `tenant_performance_metrics` (in global analytics).

### `users`

Stores information about platform users (Super Admin). Tenant users will be in tenant databases.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `name`: `VARCHAR(255)`
- `email`: `VARCHAR(255)` - `UNIQUE`, `INDEX`
- `email_verified_at`: `TIMESTAMP` - `NULLABLE`
- `password`: `VARCHAR(255)`
- `remember_token`: `VARCHAR(100)` - `NULLABLE`
- `is_super_admin`: `BOOLEAN` - Indicates if the user is a super admin. `DEFAULT 0`, `INDEX`
- `created_at`: `TIMESTAMP`
- `updated_at`: `TIMESTAMP`

## Tenant-Specific Tables

These tables will reside within each tenant's individual database.

### `products`

Stores product information for a tenant's store.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `name`: `VARCHAR(255)` - `INDEX` (if frequently searched by name)
- `description`: `TEXT` - `NULLABLE`
- `price`: `DECIMAL(10, 2)` - The price of the product.
- `category_id`: `BIGINT UNSIGNED` - Foreign key referencing the `categories` table. `NULLABLE`, `INDEX`
- `image_url`: `VARCHAR(255)` - URL to the product image. `NULLABLE`
- `total_views`: `INT UNSIGNED` - Total times the product has been viewed. `DEFAULT 0`
- `total_sales`: `INT UNSIGNED` - Total units of the product sold. `DEFAULT 0`
- `created_at`: `TIMESTAMP`
- `updated_at`: `TIMESTAMP`

**Relationships:**

- One `category` has many `products`.
- One `product` has many `order_items`.
- One `product` has many `product_views`.

### `categories`

Stores product categories for a tenant's store.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `name`: `VARCHAR(255)` - `INDEX`
- `created_at`: `TIMESTAMP`
- `updated_at`: `TIMESTAMP`

**Relationships:**

- One `category` has many `products`.

### `customers`

Stores customer information for each tenant's store.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `name`: `VARCHAR(255)`
- `email`: `VARCHAR(255)` - `UNIQUE`, `INDEX`
- `password`: `VARCHAR(255)`
- `created_at`: `TIMESTAMP`
- `updated_at`: `TIMESTAMP`

**Relationships:**

- One `customer` has many `orders`.
- One `customer` has many `shipping_addresses`.
- One `customer` has many `product_views`.

### `orders`

Stores order information for a tenant's store.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `customer_id`: `BIGINT UNSIGNED` - Foreign key referencing the `customers` table. `INDEX`
- `order_date`: `TIMESTAMP` - `INDEX`
- `total_amount`: `DECIMAL(10, 2)` - The total amount of the order.
- `payment_status`: `VARCHAR(50)` - Status of the payment (e.g., 'pending', 'completed', 'failed'). `INDEX`
- `shipping_address_id`: `BIGINT UNSIGNED` - Foreign key referencing the `shipping_addresses` table. `NULLABLE`, `INDEX`
- `total_items`: `INT UNSIGNED` - Total number of items in the order.
- `discount_amount`: `DECIMAL(10, 2)` - Total discount applied to the order. `DEFAULT 0.00`
- `tax_amount`: `DECIMAL(10, 2)` - Total tax amount for the order. `DEFAULT 0.00`
- `created_at`: `TIMESTAMP`
- `updated_at`: `TIMESTAMP`

**Relationships:**

- One `order` belongs to a `customer`.
- One `order` has many `order_items`.
- One `order` has one `payment`.
- One `order` has one `shipping_address` (optional).

### `order_items`

Stores individual items within an order for a tenant's store.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `order_id`: `BIGINT UNSIGNED` - Foreign key referencing the `orders` table. `INDEX`
- `product_id`: `BIGINT UNSIGNED` - Foreign key referencing the `products` table. `INDEX`
- `quantity`: `INT UNSIGNED`
- `price`: `DECIMAL(10, 2)` - Price of the product at the time of order.
- `created_at`: `TIMESTAMP`
- `updated_at`: `TIMESTAMP`

**Relationships:**

- One `order_item` belongs to an `order`.
- One `order_item` belongs to a `product`.

### `payments`

Stores payment transaction details for a tenant's store.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `order_id`: `BIGINT UNSIGNED` - Foreign key referencing the `orders` table. `NULLABLE`, `INDEX`
- `transaction_id`: `VARCHAR(255)` - Transaction ID from the payment gateway. `UNIQUE`, `INDEX`
- `payment_method`: `VARCHAR(100)`
- `amount`: `DECIMAL(10, 2)` - Amount of the transaction.
- `status`: `VARCHAR(50)` - Status of the transaction (e.g., 'succeeded', 'failed', 'refunded'). `INDEX`
- `gateway_response`: `TEXT` - Raw response from the payment gateway (optional). `NULLABLE`
- `created_at`: `TIMESTAMP`
- `updated_at`: `TIMESTAMP`

**Relationships:**

- One `payment` belongs to an `order` (optional).

### `shipping_addresses`

Stores shipping information for orders in a tenant's store.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `customer_id`: `BIGINT UNSIGNED` - Foreign key referencing the `customers` table. `NULLABLE`, `INDEX`
- `address`: `VARCHAR(255)`
- `city`: `VARCHAR(100)`
- `state`: `VARCHAR(100)` - `NULLABLE`
- `zip_code`: `VARCHAR(20)`
- `country`: `VARCHAR(100)`
- `created_at`: `TIMESTAMP`
- `updated_at`: `TIMESTAMP`

**Relationships:**

- One `shipping_address` belongs to a `customer` (optional).
- One `shipping_address` belongs to an `order` (optional).

### `settings`

Stores tenant-specific settings.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `store_name`: `VARCHAR(255)`
- `logo_url`: `VARCHAR(255)` - URL to the store logo. `NULLABLE`
- `contact_email`: `VARCHAR(255)` - `NULLABLE`
- `currency`: `VARCHAR(10)` - Currency used in the store (e.g., 'USD'). `DEFAULT 'USD'`
- `created_at`: `TIMESTAMP`
- `updated_at`: `TIMESTAMP`

### `product_views`

Tracks individual product views for analytics.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `product_id`: `BIGINT UNSIGNED` - Foreign key referencing the `products` table. `INDEX`
- `customer_id`: `BIGINT UNSIGNED` - Foreign key referencing the `customers` table. `NULLABLE`, `INDEX` (for guest views)
- `viewed_at`: `TIMESTAMP` - `INDEX`
- `created_at`: `TIMESTAMP`
- `updated_at`: `TIMESTAMP`

**Relationships:**

- One `product_view` belongs to a `product`.
- One `product_view` belongs to a `customer` (optional).


## Global Analytics Tables

These tables will store aggregated data from all tenants for platform-level analytics and reporting for the Super Admin.

### `platform_sales_summary`

Aggregates sales data across all tenants.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `date`: `DATE` - The date for which the data is summarized. `INDEX`
- `total_revenue`: `DECIMAL(12, 2)` - Total revenue generated across all tenants for the given date.
- `total_orders`: `INT UNSIGNED` - Total number of orders placed across all tenants for the given date.
- `average_order_value`: `DECIMAL(10, 2)` - The average value of orders across all tenants for the given date.
- `created_at`: `TIMESTAMP` - Timestamp when the record was created.
- `updated_at`: `TIMESTAMP` - Timestamp when the record was last updated.

### `tenant_performance_metrics`

Tracks key performance indicators for each tenant.

- `id`: `BIGINT UNSIGNED AUTO_INCREMENT` - Primary key.
- `tenant_id`: `BIGINT UNSIGNED` - Foreign key referencing the `tenants` table in the global database. `INDEX`
- `date`: `DATE` - The date or reporting period for which the metrics are calculated. `INDEX`
- `total_sales`: `DECIMAL(10, 2)` - Total sales generated by the tenant for the given period.
- `number_of_orders`: `INT UNSIGNED` - Total number of orders placed with the tenant for the given period.
- `new_customers`: `INT UNSIGNED` - Number of new customers acquired by the tenant during the given period.
- `created_at`: `TIMESTAMP` - Timestamp when the record was created.
- `updated_at`: `TIMESTAMP` - Timestamp when the record was last updated.

**Relationships:**

- One `tenant_performance_metric` belongs to a `tenant`.

## Sample Data Examples

Here are a few examples of how data might be structured in some of the tables:

### `tenants`

| id | uuid                                 | name            | slug         | domain          | database          | is_active | created_at          | updated_at          |
|----|--------------------------------------|-----------------|--------------|-----------------|-------------------|-----------|---------------------|---------------------|
| 1  | 550e8400-e29b-41d4-a716-446655440000 | Fresh Mart      | fresh-mart   | freshmart.com   | tenant_uuid_1     | 1         | 2023-10-27 10:00:00 | 2023-10-27 10:00:00 |
| 2  | a1b2c3d4-e5f6-7890-1234-567890abcdef | Go Grocery      | go-grocery   | gogrocery.io    | tenant_uuid_2     | 1         | 2023-10-27 11:00:00 | 2023-10-27 11:00:00 |

### `users`

| id | name         | email                | email_verified_at | password                                                      | remember_token | is_super_admin | created_at          | updated_at          |
|----|--------------|----------------------|-------------------|---------------------------------------------------------------|----------------|----------------|---------------------|---------------------|
| 1  | Admin User   | admin@platform.com   | 2023-10-27 09:00:00 | $2y$10$... (hashed password)                                | NULL           | 1              | 2023-10-27 09:00:00 | 2023-10-27 09:00:00 |

### `products` (Tenant-Specific)

| id | name         | description                      | price | category_id | image_url          | total_views | total_sales | created_at          | updated_at          |
|----|--------------|----------------------------------|-------|-------------|--------------------|-------------|-------------|---------------------|---------------------|
| 1  | Apples       | Fresh red apples                 | 1.99  | 1           | /images/apples.jpg | 150         | 50          | 2023-10-27 12:00:00 | 2023-10-27 12:00:00 |
| 2  | Bananas      | Organic bananas                  | 0.79  | 1           | /images/bananas.jpg| 200         | 80          | 2023-10-27 12:10:00 | 2023-10-27 12:10:00 |

### `orders` (Tenant-Specific)

| id | customer_id | order_date          | total_amount | payment_status | shipping_address_id | total_items | discount_amount | tax_amount | created_at          | updated_at          |
|----|-------------|---------------------|--------------|----------------|---------------------|-------------|-----------------|------------|---------------------|---------------------|
| 1  | 1           | 2023-10-27 14:00:00 | 5.50         | completed      | 1                   | 3           | 0.00            | 0.50       | 2023-10-27 14:00:00 | 2023-10-27 14:00:00 |

### `platform_sales_summary` (Global Analytics)

| id | date       | total_revenue | total_orders | average_order_value | created_at          | updated_at          |
|----|------------|---------------|--------------|---------------------|---------------------|---------------------|
| 1  | 2023-10-27 | 1500.75       | 120          | 12.51               | 2023-10-28 00:00:00 | 2023-10-28 00:00:00 |
