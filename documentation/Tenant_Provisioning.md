# Tenant Provisioning Process Documentation

## Overview

This document outlines the process for provisioning a new tenant (grocery vendor) on the SaaS platform. It covers the steps involved from a vendor signing up to having their own functional online store instance.
This is an automated process triggered by a successful vendor registration.

## Steps Involved

This section details the sequence of actions that occur when a new vendor signs up and their tenant environment is created. This includes user registration, subdomain or custom domain setup, database creation and migration, initial configuration, and subscription activation.

1.  **Vendor Registration:**
    *   The potential vendor submits a registration form on the platform's website, providing necessary initial information such as store name, email address, password, and desired subdomain (if applicable).
    *   Basic validation of the provided information is performed.

2.  **Create Tenant Record (Global Database):**
    *   A new record is created in the global `tenants` table (in the platform's main database).
    *   This record includes a unique identifier (e.g., a UUID), the chosen subdomain or initial domain, and other basic tenant information.

3.  **Create Tenant Database:**
    *   A new, separate database instance is dynamically created for this specific tenant.
    *   The database name typically incorporates the tenant's unique identifier (e.g., `tenant_<uuid>`) to ensure isolation.

4.  **Run Tenant Migrations:**
    *   Laravel database migrations specifically designed for tenant databases are executed on the newly created database.
    *   This sets up all the necessary tenant-specific tables (e.g., `products`, `orders`, `customers`, `settings`, etc.) within that tenant's database.

5.  **Configure Subdomain/Domain:**
    *   **Subdomain:** If the vendor chose a subdomain, the system updates the web server configuration (e.g., Nginx) to route requests for that subdomain to the application and associate the request with the correct tenant.
    *   **Custom Domain:** If the vendor intends to use a custom domain, instructions are provided for them to configure their domain's DNS records (A or CNAME) to point to the platform's servers. The system may periodically check for DNS propagation.

6.  **Initial Configuration:**
    *   Default or initial settings are populated in the tenant's `settings` table (e.g., initial store name, default currency, contact email placeholder).
    *   Basic data, if required, might be seeded into other tenant tables.

7.  **Integrate with Subscription:**
    *   The new tenant record is linked to a subscription plan, often starting with a trial period.
    *   This involves interaction with the Subscriptions module, potentially creating a customer record in Stripe via Laravel Cashier.

8.  **Send Welcome/Onboarding Notifications:**
    *   Automated emails are sent to the vendor.
    *   This includes a welcome message, confirmation of their store URL (subdomain or initial custom domain), and potentially a link to the Tenant Admin Panel and onboarding guides.

## Potential Challenges

This section identifies potential issues or hurdles that might arise during the tenant provisioning process. This could include domain name conflicts, database provisioning errors, subscription payment failures, or configuration inconsistencies.

-   **Database Creation and Migration Errors:** Issues with database server permissions, resource limitations, or errors within the migration files can halt the process.
-   **DNS Propagation Delays:** Especially with custom domains, there can be delays in DNS records updating across the internet, preventing the domain from correctly pointing to the platform immediately.
-   **Web Server Configuration Issues:** Errors in updating Nginx (or other web server) configurations for new subdomains or custom domains can prevent the store from being accessible.
-   **Concurrency Issues:** Handling multiple vendors signing up simultaneously requires careful management to avoid race conditions or resource conflicts during database creation and configuration.
-   **Data Consistency:** Ensuring that the global tenant record, the tenant database, and any related service configurations (like in the Payments or Subscriptions modules) are all correctly linked and consistent throughout the provisioning process.
-   **Failure Handling and Reversion:** Implementing a robust mechanism to detect failures at any step and either retry the step or cleanly revert the entire provisioning process to avoid leaving behind incomplete or corrupted tenant instances.
-   **Resource Limits:** Hitting limits on database connections, storage space, or other server resources during mass provisioning.