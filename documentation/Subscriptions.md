# Subscriptions Module Documentation

## Overview

This document outlines the functionality and implementation details of the Subscriptions module within the SaaS Grocery Platform. This module handles the platform-level subscriptions for vendors, allowing them to subscribe to different plans to use the platform's services.

## Features

*   **Subscription Plan Management:** Defines different subscription tiers (e.g., Free, Basic, Pro) with varying features and pricing.
*   **Vendor Plan Selection:** Allows vendors to select a subscription plan during the onboarding process.
*   **Plan Upgrade/Downgrade:** Enables vendors to easily upgrade or downgrade their subscription plans.
*   **Billing Integration:** Integrates with a payment gateway (like Stripe Cashier) for handling recurring billing and payments.
*   **Trial Periods:** Support for offering trial periods to new vendors.
*   **Subscription Status Tracking:** Tracks the status of vendor subscriptions (active, trial, canceled, past due).
*   **Invoice Generation:** Automatic generation of invoices for subscription payments.
*   **Billing History:** Provides vendors with access to their past invoices and billing history.
*   **Failed Payment Handling:** Implements logic to handle failed subscription payments and dunning.
*   **Subscription Cancellation:** Allows vendors to cancel their subscriptions.
*   **Proration Calculation:** Handles proration for plan upgrades or downgrades.
*   **Payment Method Updates:** Allows vendors to update their payment information for subscriptions.

## Implementation Details

The Subscriptions module will leverage Laravel Cashier (Stripe) for managing vendor subscriptions and billing. This will involve setting up Stripe products and plans that correspond to our platform's subscription tiers.

Subscription data will be stored in the global database, associated with the `tenants` table, as subscriptions are platform-level and not specific to a tenant's individual store data.

Key implementation aspects will include:

*   **Database Design:** Migrations and models for `subscriptions` and related tables to store subscription information.
*   **Stripe Webhooks:** Setting up webhooks to handle events from Stripe (e.g., successful payment, subscription renewal, failed payment).
*   **User Interface:** Creating pages in the Super Admin panel to manage subscription plans and in the Vendor Admin panel for vendors to manage their subscriptions.
*   **Integration with other Modules:** Ensuring that features in other modules (like Tenant Admin Panel) are enabled or disabled based on the vendor's current subscription plan.
*   **Feature Gating:** Implementing logic within the application to control access to features based on the vendor's current subscription plan and its entitlements.
*   **Error Handling:** Implementing robust error handling for payment failures and subscription issues.
*   **Edge Case Management:** Carefully handling edge cases related to trials, cancellations, and payment failures.