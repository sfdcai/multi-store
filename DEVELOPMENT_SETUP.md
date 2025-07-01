# Development Setup

This document outlines the steps to set up the development environment and build the SaaS Grocery Platform.

## Step 1: Configure the Development Environment

The first step is to configure the development environment with the necessary packages for Laravel development. This is done by updating the `.idx/dev.nix` file.

The following packages were added:
- `pkgs.php82`: The PHP 8.2 interpreter.
- `pkgs.php82Packages.composer`: The Composer dependency manager for PHP.
- `pkgs.nodejs_20`: Node.js version 20 for frontend asset management.

The following VS Code extensions were also added to improve the development experience:
- `bmewburn.vscode-intelephense-client`: PHP Intelephense for code intelligence.
- `onecentlin.laravel-blade`: Laravel Blade support.
- `ms-azuretools.vscode-docker`: Docker support.

After updating the `.idx/dev.nix` file, the environment was rebuilt to apply the changes.
