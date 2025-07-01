# Vendor Onboarding Module Documentation

## Overview

The Vendor Onboarding module is responsible for the process of bringing new grocery vendors onto the SaaS platform. This includes vendor registration, setting up their initial store environment, configuring their subdomains or custom domains, and provisioning their dedicated database or configuring shared database tenancy.

## Features

- **Vendor Registration:** Allows new vendors to sign up for the platform, providing necessary business information.
- **Tenant Provisioning:** Automatically sets up the necessary infrastructure for a new vendor's store, including database and file storage.
- **Domain/Subdomain Configuration:** Manages the assignment of a subdomain (e.g., `vendorname.yourplatform.com`) or the process for the vendor to map a custom domain to their store.
- **Initial Store Setup:** Guides the vendor through initial configuration steps for their store, such as setting the store name, logo, and basic contact information.
- **Subscription Management Integration:** Initiates the process of setting up the vendor's subscription plan and integrating with the payment gateway (e.g., Stripe Cashier).

## Implementation Details

The Vendor Onboarding process will primarily involve a series of web forms for vendor registration and configuration. Upon successful registration and potentially payment confirmation, backend processes will be triggered to:

- Create a new tenant record in the central database.
- Provision a new database for the tenant (if using database per tenant) or create necessary schema/identifiers (if using shared database).
- Configure DNS records for the vendor's subdomain.
- Set up initial configurations in the tenant's environment.
- Interact with the subscription management system (Stripe Cashier) to create a new customer and subscription.
- Send a welcome email to the new vendor.

Key components involved will include Laravel controllers, service classes for handling tenant provisioning logic, database migrations for tenant schemas, and integration with the chosen tenancy package (Stancl/Tenancy) and payment gateway library (Laravel Cashier/Stripe).