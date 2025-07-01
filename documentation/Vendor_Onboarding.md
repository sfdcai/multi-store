# Vendor Onboarding Module Documentation

## Overview

The Vendor Onboarding module is responsible for the process of bringing new grocery vendors onto the SaaS platform. This includes vendor registration, setting up their initial store environment, configuring their subdomains or custom domains, and provisioning their dedicated database or configuring shared database tenancy.

## Features

- **Vendor Registration:** Allows new vendors to sign up for the platform, providing necessary business information.
- **User Registration Form:** A web form for vendors to input their details and create an account.
- **Validation:** Server-side validation of submitted vendor information to ensure data integrity and security.
- **Tenant Record Creation:** Creation of a new record in the global `tenants` table to represent the new vendor's store.
- **Tenant Database Provisioning:** Automated setup of a dedicated database for the new tenant (if using database per tenant) or configuration within a shared database.
- **Domain Mapping:** Configuration of a subdomain (e.g., `vendorname.yourplatform.com`) or the process for the vendor to map a custom domain to their store.
- **Subscription Integration:** Integration with the subscription management module (using Laravel Cashier) to select and initiate a subscription plan for the new vendor.
- **Welcome Emails:** Sending automated welcome emails and onboarding instructions to the newly registered vendor.

## Implementation Details

The Vendor Onboarding process will primarily involve a series of web forms for vendor registration and configuration. Upon successful registration and potentially payment confirmation, backend processes will be triggered to:

- Create a new tenant record in the central database.
- Using **Laravel's authentication scaffolding** for handling user registration and login.
- Leveraging the **`stancl/tenancy`** package for creating new tenant records and managing tenant-specific databases or schema separation.
- Utilizing **Laravel's database migration capabilities** to set up the necessary tables and schema within the new tenant's database.
- Configure DNS records for the vendor's subdomain.
- Set up initial configurations in the tenant's environment.
- Interact with the subscription management system (Stripe Cashier) to create a new customer and subscription.
- Send a welcome email to the new vendor.

Key components involved will include Laravel controllers, service classes for handling tenant provisioning logic, database migrations for tenant schemas, and integration with the chosen tenancy package (Stancl/Tenancy) and payment gateway library (Laravel Cashier/Stripe).
