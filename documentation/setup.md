# Setup & Preconfiguration

This guide covers the necessary steps to prepare your environment before deploying the Radius Billing System to K3s.

## 1. Hardware Requirements

Thanks to the Alpine Linux optimization, the requirements are minimal:

*   **Processor**: 1 vCPU (x86_64 or ARM64).
*   **Memory**: 1 GB RAM (Minimum), 2 GB RAM (Recommended).
*   **Disk**: 10 GB SSD.

## 2. Pre-deployment Configuration

### Environment Variables (`.env`)
Before deploying, you must create a `.env` file from the example. This file stores your secrets.

```bash
cp .env.example .env
nano .env
```

**Critical Variables to Set:**
*   `DB_PASSWORD`: Strong password for the MySQL database.
*   `RADIUS_SECRET`: Shared secret between FreeRADIUS and your Mikrotik.
*   `MPESA_*`: Your Safaricom Developer credentials.
*   `MONGO_*`: (Only if you decide to keep the optional UniFi service).

### Database Initialization
The `mysql` service requires an initial schema to function. 

*   **Automation**: These files are included in the repository and are automatically mounted into the `/docker-entrypoint-initdb.d/` directory of the MySQL container.
*   **Action**: On the first boot, the container executes these SQL scripts to create the necessary tables for both the Billing Portal and FreeRADIUS.
*   **Verification**: You can verify the files are present in `conf/mysql.sh` and `raddb/config_data/mods-config/sql/main/mysql/schema.sql`.

## 3. Building Optimized Images

We recommend using the **Alpine** versions of the Dockerfiles for the smallest footprint.

```bash
# Build Alpine-based images
docker build -t radius-app:latest-alpine -f Dockerfile.alpine .
docker build -t radius-freeradius:latest-alpine -f raddb/Dockerfile.alpine ./raddb/
docker build -t radius-coa:latest-alpine -f coa-service/Dockerfile.alpine ./coa-service/
```

## 4. K3s Installation

If you don't have K3s installed, run:

```bash
curl -sfL https://get.k3s.io | sh -
# Verify the cluster
sudo k3s kubectl get nodes
```

### Importing Images
If you are not using a remote registry, import the images directly into K3s:

```bash
docker save radius-app:latest-alpine | sudo k3s ctr images import -
docker save radius-freeradius:latest-alpine | sudo k3s ctr images import -
docker save radius-coa:latest-alpine | sudo k3s ctr images import -
```

## 5. Deployment

Apply the manifests in the following order:

1.  `namespace.yaml`
2.  `secrets.yaml` (Update this with your base64 encoded secrets from `.env`)
3.  `mysql.yaml`
4.  `app.yaml`
5.  `freeradius.yaml`
6.  `coa-service.yaml`

```bash
sudo k3s kubectl apply -f k3s/
```
