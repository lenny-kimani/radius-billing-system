# Mikrotik L2TP VPN Guide

Exposing RADIUS (UDP 1812/1813) to the public internet is insecure. To protect your ISP operations, we use an **L2TP/IPsec VPN** to connect remote Mikrotik routers to the central RADIUS server.

## 1. Why L2TP/IPsec?
*   **Security**: All RADIUS traffic is encrypted within an IPsec tunnel.
*   **Routing**: The Mikrotik behaves as if it is on the local network of the RADIUS server.
*   **Stability**: Highly compatible with Mikrotik's RouterOS.

## 2. Server Setup (K3s Host)

You should install an L2TP server on the host machine running K3s. A common choice is `accel-ppp` or the built-in VPN features of your cloud provider.

**Example Configuration (Conceptual):**
*   **VPN Subnet**: `10.8.0.0/24`
*   **K3s Node IP**: `10.8.0.1` (Gateway)
*   **Mikrotik VPN IP**: `10.8.0.10`

## 3. Mikrotik Configuration

### Step A: Create the L2TP Client
Go to **PPP** -> **Interface** -> **Add** -> **L2TP Client**.

*   **Connect To**: Your K3s Server Public IP.
*   **User/Password**: Your VPN credentials.
*   **Use IPsec**: Yes.
*   **IPsec Secret**: Your strong pre-shared key.

### Step B: Configure RADIUS
Go to **RADIUS** -> **Add**.

*   **Service**: hotspot, pppoe.
*   **Address**: `10.8.0.1` (The VPN IP of the K3s host).
*   **Secret**: The `RADIUS_SECRET` from your `.env` file.
*   **Src. Address**: `10.8.0.10` (The Mikrotik's VPN IP).

### Step C: Firewall Rules
Ensure the K3s host allows UDP 1701, 500, and 4500 for the VPN, and that the internal firewall allows traffic from the `10.8.0.0/24` subnet to reach the FreeRADIUS container.

## 4. Verification

On the Mikrotik terminal:
```routeros
/radius monitor [find]
```
You should see "pending requests: 0" and "received responses" incrementing when users attempt to log in.
