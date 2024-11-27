# AWX Ascender Installation and Updating on K3s for Rocky 9 or RHEL 9

## General Prerequisites

Make sure you have completed the general prerequisites outlined in the main README of the Ascender-Install repository.

## K3s-specific Prerequisites

**Note**: The Ascender K3s setup is primarily intended for sandbox or testing purposes and is not production-ready. This installation is intended for a single-node K3s cluster that serves as both the master and worker node.

- **Operating System Requirements**: The OS family must be similar to **Rocky Linux** (based on the `ansible_os_family` fact), and the version must be 8 or 9. The installer has primarily been tested with **Rocky Linux**.
- **Minimum System Requirements**:
  - **CPU**: 2 cores
  - **Memory**: 8GB (required if installing both Ascender and Ledger)
  - **Storage**: At least 20GB of free disk space (for Ascender and Ledger volumes)
- The installer can either set up a new K3s cluster or use an existing one, based on the value of the `kube_install` variable:
  - Set **`kube_install` to `true`** if you want the installer to set up a new K3s cluster on the target host.
  - Set **`kube_install` to `false`** if you already have K3s running.
- **SSL Certificate and Key**: For enabling HTTPS, you need to provide an **SSL certificate** and **private key**. These can be self-signed or issued by a trusted authority, such as **Let's Encrypt**. Specify the certificate and key locations in the default configuration file (`tls_crt_path` and `tls_key_path`). The installer will use them to create a Kubernetes TLS secret.

## Installation and Upgrade Instructions

### Step 1: Obtain the Installer Sources

1. **Clone the Ascender Installer Repository**
   ```bash
   git clone https://github.com/ctrliq/ascender-install.git
   ```
   This will create a directory named `ascender-install` in your current working directory.

2. **Change Directory to the Installer Folder**
   ```bash
   cd ascender-install
   ```

### Step 2: Configure Variables for K3s Installation

1. **Run the Configuration Script**
   ```bash
   ./config_vars.sh
   ```
   This script will prompt you for input to populate the necessary configuration variables, which will be saved in `custom.config.yml`.

2. **Edit the Configuration Manually** (Optional)
   - You can manually adjust the settings in `custom.config.yml` as needed before reinstalling or upgrading Ascender.

3. **Example Configuration Files**
   - Sample configuration files for online and offline installations (`k3s.default.config.yml` and `k3s.offline.default.config.yml`) can be found in this directory.

### Step 3: Run the Setup Script

1. **Run the Setup Script with Administrative Privileges**
   ```bash
   sudo ./setup.sh
   ```
   After completing the setup successfully, you should see output similar to:
   ```
   ASCENDER SUCCESSFULLY SETUP
   ```

## Offline Installation Instructions

To perform an offline installation of Ascender on K3s:

1. **Run `create_bundle.yml` Playbook** on a machine with internet access to create the necessary bundle.
2. **Copy the `offline` Folder** into your `ascender-install` directory on the target machine.
3. **Set Offline Variables**:
   - In `config_vars.sh`, set `k8s_platform` to **k3s** and `k8s_offline` to **true** to indicate that archived container images should be used instead of pulling from a container registry.

The installer will import these images into the K3s server and set `imagePullPolicy` to **Never** to ensure no external connections are attempted during the installation.

## Offline Upgrade Instructions

To upgrade an offline installation of Ascender on K3s:

1. **Re-run `create_bundle.yml` Playbook** on a machine with internet access, specifying the release or tag (`ASCENDER_VERSION`) that you want to upgrade to in the configuration file (`custom.config.yml` or `default.config.yml`).
2. **Copy the `offline` Folder** into your `ascender-install` directory on the target machine.
3. **Set Offline Variables for Upgrade**:
   - Set `k8s_platform` to **k3s** and `k8s_offline` to **true**.

## Connecting to Ascender Web UI

1. **Get the Cluster IP Address**
   ```bash
   export ASCENDER_WEB_INTERNAL_IP=$(kubectl -n ascender get service/ascender-app-service -o jsonpath='{.spec.clusterIP}')
   ```

2. **Verify the IP Address**
   ```bash
   echo $ASCENDER_WEB_INTERNAL_IP
   ```

3. **SSH Port Forwarding to Access Ascender**
   ```bash
   ssh -L 80:<ASCENDER_WEB_INTERNAL_IP>:80 user@<ASCENDER_SERVER_IP>
   ```
   For example, if the `ASCENDER_WEB_INTERNAL_IP` is `10.43.9.224` and `ASCENDER_SERVER_IP` is `1.2.3.4`, use the following command:
   ```bash
   ssh -L 80:10.43.9.224:80 root@1.2.3.4
   ```

4. **Access the Web Interface**
   - After forwarding, open your browser and go to `https://localhost`.
   - The default username is **admin**. The password can be found in `<ASCENDER-INSTALL-SOURCE>/default.config.yml` under the `ASCENDER_ADMIN_PASSWORD` variable.

## Uninstall Instructions

1. **Delete Kubernetes Manifests**
   - After running `setup.sh`, the `tmp_dir` (default: `{{ playbook_dir }}/../ascender_install_artifacts`) will contain timestamped Kubernetes manifests:
     - `ascender-deployment-{{ k8s_platform }}.yml`
     - `ledger-{{ k8s_platform }}.yml` (if Ledger was installed)
     - `kustomization.yml`

2. **Remove the Timestamp and Run the Following Commands** from within `tmp_dir`:
   ```bash
   kubectl delete -f ascender-deployment-{{ k8s_platform }}.yml
   kubectl delete -f ledger-{{ k8s_platform }}.yml  # Optional if Ledger was installed
   kubectl delete -k .
   ```

Running these commands will remove all related deployments and stateful sets. However, **persistent volumes and secrets** will remain. To remove secrets, set `ascender_garbage_collect_secrets: true` in `default.config.yml`.

## Conclusion

The **Ascender Installer** with **K3s** on **Rocky 9 or RHEL 9** provides an efficient and easy way to set up a sandbox environment for exploring the Ascender Automation Platform. This guide outlines steps for both online and offline installations, connecting to the web UI, and clean uninstallation.

[Tutorial Here:](https://www.youtube.com/watch?v=lswN7Ct1cjE)
[Documentaton on K3S Here:](https://docs.k3s.io/quick-start)
[Documentation for Ascender here:](https://github.com/ctrliq/ascender-install/blob/main/docs/k3s/README.md#k3s-specific-prerequisites)
