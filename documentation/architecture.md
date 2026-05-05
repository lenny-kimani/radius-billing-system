# Architecture & Optimization

The Radius Billing System has transitioned from a heavy, multi-purpose stack to a highly optimized, core-only architecture.

## 1. The Core Stack
The system now focuses exclusively on the essential components required for an ISP billing operation:

*   **App (PHPNuxBill)**: The customer and admin portal. Optimized with **PHP 8.2-FPM on Alpine Linux**.
*   **MySQL (MariaDB)**: Unified storage for users, plans, vouchers, and RADIUS data.
*   **FreeRADIUS**: The authentication engine. Optimized with **Alpine Linux** for minimal memory usage.
*   **CoA Service**: Handles session disconnection (Change of Authorization). Lightweight PHP-CLI on Alpine.

## 2. Removed Components
To meet my setup requirements the following optional services were removed from the default K3s/Production setup:

| Component | Reason for Removal | Resources Saved |
|-----------|--------------------|-----------------|
| **UniFi Application** | Optional network controller; requires heavy Java runtime. | ~600 MB RAM |
| **Omada Controller** | Optional network controller. | ~500 MB RAM |
| **MongoDB** | Only required by UniFi; incompatible with MariaDB. | ~400 MB RAM |

> [!NOTE]
> If you require these controllers, they should be deployed as separate workloads to avoid impacting the stability and performance of the core billing system.

## 3. Alpine Linux Migration
Switching from Ubuntu/Debian to Alpine Linux has drastically reduced the "noise" in our containers.

*   **Security**: Minimal base image means fewer installed binaries and a smaller attack surface.
*   **Speed**: Faster boot times and significantly smaller image sizes (~140MB vs ~600MB).
*   **Memory**: Alpine uses `musl libc` and `busybox`, which have a much lower memory overhead than `glibc` and GNU coreutils.

## 4. K3s Design
The K3s manifests utilize `hostNetwork: true` for FreeRADIUS. This is intentional to ensure that UDP RADIUS traffic (ports 1812, 1813) reaches the container without the overhead and potential NAT issues of the standard Kubernetes Service mesh.
