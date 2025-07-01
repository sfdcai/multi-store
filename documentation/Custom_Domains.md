# Custom Domains Module Documentation

## Overview

The Custom Domains module allows vendors to map their own domain name to their online store hosted on the SaaS platform. This provides a professional and branded experience for their customers, making their store appear as a standalone website rather than a subdomain of the main platform.

## Features

- **DNS Instructions for Vendors:** Clear, step-by-step instructions provided to vendors on how to point their domain's DNS records (e.g., CNAME or A records) to the platform's servers.
- **DNS Validation:** Functionality to automatically check and validate that the vendor's DNS records have been correctly configured and propagated.
- **Domain Association with Tenant:** Securely linking the custom domain to the specific vendor's tenant account in the platform's global database.
- **SSL Certificate Provisioning:** Automated provisioning of free SSL certificates (likely using Let's Encrypt) for each custom domain to enable HTTPS.
- **SSL Renewal:** Automated renewal of SSL certificates before they expire to ensure continuous secure access.
- **Switching to Subdomain:** An option for vendors to easily revert back to using their default subdomain if they choose to.
- **Handling Conflicts/Errors:** Robust error handling and clear reporting to vendors if there are issues with domain configuration, validation, or SSL provisioning.

## Implementation Details

The implementation will involve several key technical components:
- **Laravel Routing:** Using Laravel's routing capabilities to identify the requested domain and resolve it to the correct tenant's context using the `stancl/tenancy` package.
- **DNS Validation Methods:** Implementing methods to programmatically check DNS records. This could involve using external DNS lookup libraries or interacting with DNS provider APIs if available.
- **SSL Provisioning Service/Library:** Integrating with a service like Certbot or a cloud provider's SSL management system for automated SSL certificate issuance, validation (e.g., using HTTP-01 or DNS-01 challenges), and renewal.
- **Storing Domain/SSL Info Globally:** Storing custom domain names, validation status, SSL certificate status, and expiration dates in the global database, linked to the `tenants` table.
- **Web Server Configuration (Nginx):** Configuring the web server (Nginx) to dynamically handle requests for different domains and serve the correct application instance, potentially using server block configurations generated or managed by the application or a deployment tool.
- **Handling Misconfigurations:** Implementing error pages and logging for cases where a custom domain is pointed incorrectly, SSL is invalid, or other configuration issues occur.