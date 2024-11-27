# Setting Up AWX on Rocky 9 or RHEL 9 using k3s and Ascender Installer

## Prerequisites

1. **Update Rocky 9 or RHEL 9 System**
   ```bash
   sudo dnf update -y
   ```

2. **Install Dependencies**
   ```bash
   sudo dnf install -y git curl podman
   ```

3. **Install k3s (Lightweight Kubernetes)**
   
   Install the lightweight Kubernetes cluster:
   ```bash
   curl -sfL https://get.k3s.io | sh -
   ```
   Validate the installation:
   ```bash
   kubectl version
   ```
   ```bash
   kubectl get nodes
   ```
   This should show your node in a `Ready` state.

5. **Verify k3s Configuration**

   Ensure the `kubectl` command is configured to use k3s:
   ```bash
   sudo chown $(whoami): /etc/rancher/k3s/k3s.yaml
   export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
   ```

## Step 1: Clone the Ascender Installer Repository

1. **Clone the Repository**
   ```bash
   git clone https://github.com/ctrliq/ascender-install.git
   cd ascender-install
   ```

## Step 2: Install Ascender

1. **Run the Installation Script**
   ```bash
   sudo ./install.sh
   ```
   This script will set up Ascender, including deploying any dependencies required on Kubernetes.

## Step 3: Deploy AWX Using Ascender

1. **Set k3s as the Kubernetes Cluster**

   Ensure Ascender uses your k3s configuration by exporting the `KUBECONFIG` environment variable:
   ```bash
   export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
   ```

2. **Deploy AWX**
   Run the deployment command:
   ```bash
   ./ascender.sh deploy
   ```
   Ascender will:
   - Deploy the AWX Operator
   - Create necessary namespaces and configurations
   - Deploy AWX and its dependencies (e.g., PostgreSQL)

3. **Monitor the Deployment**
   Use `kubectl` to verify the status of pods:
   ```bash
   kubectl get pods -n awx
   ```
   Wait until all pods are in the `Running` state.

## Step 4: Retrieve Credentials and Access AWX

1. **Get the Admin Password**

   Use this command to retrieve the auto-generated admin password:
   ```bash
   kubectl get secret awx-admin-password -n awx -o jsonpath="{.data.password}" | base64 --decode
   ```

2. **Get the AWX Service URL**

   Use `kubectl` to find the NodePort of the AWX service:
   ```bash
   kubectl get svc -n awx
   ```
   Note the external port (e.g., `NodePort`) to access AWX.

3. **Access AWX**

   Open a browser and navigate to:
   ```
   http://<your-server-ip>:<NodePort>
   ```
   Log in with:
   - **Username:** `admin`
   - **Password:** Retrieved in step 1.

## Benefits of Using Ascender with k3s

- **Simplified Installation**: Ascender automates the AWX setup, making the process straightforward.
- **Lightweight Deployment**: k3s is optimized for resource-constrained environments, perfect for home labs and development.
- **Quick and Reliable**: Ascender manages the Kubernetes configurations and deploys AWX efficiently.

## Conclusion

Using Ascender with **k3s** is the easiest and most efficient way to deploy AWX on Rocky 9 or RHEL 9. It simplifies the setup process, automates complex configurations, and provides a reliable solution without requiring deep Kubernetes knowledge.
[Tutorial Here:](https://www.youtube.com/watch?v=lswN7Ct1cjE)
[Documentaton on K3S Here:](https://docs.k3s.io/quick-start)
