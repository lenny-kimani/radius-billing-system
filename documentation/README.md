# Radius Billing System: Official Documentation

Welcome to the documentation for the optimized **Radius Billing System**. This system has been audited, hardened, and optimized for high-efficiency deployment using **K3s** and **Alpine Linux**.

## 📖 Table of Contents

1.  **[Getting Started: Preconfiguration & Setup](setup.md)**
    *   Hardware requirements, environment variables, and initial cluster setup.
2.  **[Architecture: Alpine & K3s Optimization](architecture.md)**
    *   Understanding the minimal footprint, container breakdown, and removed components.
3.  **[Networking: Mikrotik L2TP VPN Guide](mikrotik-vpn.md)**
    *   Securely connecting remote NAS/Mikrotik devices to your RADIUS server.
4.  **[Security: Audit & Hardening Summary](security-audit.md)**
    *   Overview of the security fixes (SQLi, Command Injection) implemented during the audit.

## 🚀 Key Features

*   **Ultra-Lightweight**: Entire core stack runs in **< 1 GB RAM**.
*   **Security First**: Fully audited code with parametrized queries and sanitized shell commands.
*   **M-Pesa Ready**: Production-grade M-Pesa integration with amount verification and tunnel support.
*   **Cloud Native**: Designed for K3s/Kubernetes with full manifest support.

