# Ascender Installation and Updating on K3s for Rocky 9 or RHEL 9

## General Prerequisites

If you have not done so already, be sure to follow the general prerequisites found in the Ascender-Install main README.

## K3s-specific Prerequisites

**Note**: The K3s install of Ascender is not yet meant for production, but rather as a sandbox to try Ascender. The installer expects a single-node K3s cluster, which will act as both master and worker node.

- **Operating System Requirements**: The OS Family must be the same as Rocky Linux (indicated by the `ansible_os_family` Ansible fact), and the major version must be 8 or 9. The installer is primarily tested with **Rocky Linux**.
- **Minimal System Requirements**:
  - **CPUs**: 2
  - **Memory**: 8GB (if installing both Ascender and Ledger)
  - **Disk Space**: 20GB of free disk space (for Ascender and Ledger Volumes)
- These instructions accommodate an existing K3s cluster or will set up a new one if necessary. This behavior is determined by the variable `kube_install`:
  - If `kube_install` is set to **true**, the installer will set up K3s on the `ascender_host` in the inventory file (`ascender_host` can be localhost).
  - If `kube_install` is set to **false**, the installer will not perform a K3s installation.
- **SSL Certificate and Key**: To enable HTTPS, you need to provide the Ascender installer with an **SSL Certificate** file and a **Private Key** file. These can be self-signed or issued by a trusted Certificate Authority (e.g., **Let's Encrypt**).
  - Once you have the Certificate and Private Key file, specify their locations in the default config file (`tls_crt_path` and `tls_key_path`). The installer will use these to create a Kubernetes TLS Secret for HTTPS enablement.

## Install and Upgrade Instructions

### Obtain the Sources

1. **Clone the Ascender Installer Repository**
   ```bash
   git clone https://github.com/ctrliq/ascender-install.git
   ```
   This will create a directory named `ascender-install` in your present working directory.

2. **Change Directories**
   ```bash
   cd ascender-install
   ```

### Set the Configuration Variables for a K3s Install

1. **Run the Config Vars Script**
   ```bash
   ./config_vars.sh
   ```
   The script will take you through a series of questions to populate the variables file required to install Ascender. This variables file will be created at `./custom.config.yml`.

2. **Edit the Configuration Manually** (Optional)
   - You can manually edit the `custom.config.yml` file if you want to adjust variables before (re)installing Ascender.

3. **Examples of Configuration Files**
   - Example configuration files for traditional installation (`k3s.default.config.yml`) and offline installation (`k3s.offline.default.config.yml`) can be found in this directory.

### Run the Setup Script

1. **Run the Setup Script**
   ```bash
   sudo ./setup.sh
   ```
   The setup must run as a user with **Administrative** or **sudo** privileges. Once the setup is completed successfully, you should see a final output similar to:
   ```
   ASCENDER SUCCESSFULLY SETUP
   ```

## Offline Installation

To perform an offline installation of Ascender on K3s:

1. **Run the `create_bundle.yml` Playbook** on a machine with internet access.
2. **Copy the `offline` Folder** into your `ascender-install` folder on the machine you would like to install from.
3. **Set Variables for Offline Install**:
   - When setting the variables in your `config_vars.sh`, be sure to set `k8s_platform` to **k3s** and `k8s_offline` to **true**. This will instruct the installer to use archived container images rather than pulling from a container registry.

The installer will copy the archived images to the K3s server, import them into K3s, and set the `imagePullPolicy` for all K3s images to **Never** to prevent internet access during installation.

## Offline Ascender Upgrade

To upgrade an offline installation of Ascender on K3s:

1. **Re-run the `create_bundle.yml` Playbook** on a machine with internet access, using the new release/tag indicated by the `ASCENDER_VERSION` variable in `custom.config.yml` or `default.config.yml`.
2. **Copy the `offline` Folder** into your `ascender-install` folder.
3. **Set Variables for Offline Upgrade**:
   - Set `k8s_platform` to **k3s** and `k8s_offline` to **true**.

## Connecting to Ascender Web UI

To connect to your new Ascender installation:

1. **Get the Cluster IP**
   ```bash
   export ASCENDER_WEB_INTERNAL_IP=$(kubectl -n ascender get service/ascender-app-service -o jsonpath='{.spec.clusterIP}')
   ```

2. **Verify the IP Address**
   ```bash
   echo $ASCENDER_WEB_INTERNAL_IP
   ```

3. **SSH Forwarding to Connect to Ascender**
   ```bash
   ssh -L 80:<ASCENDER_WEB_INTERNAL_IP>:80 user@<ASCENDER_SERVER_IP>
   ```
   For example, if `ASCENDER_WEB_INTERNAL_IP` is `10.43.9.224` and `ASCENDER_SERVER_IP` is `1.2.3.4`, the full command to connect as the root user will be:
   ```bash
   ssh -L 80:10.43.9.224:80 root@1.2.3.4
   ```

4. **Access the Web Interface**
   - After port forwarding, you can access the Ascender instance by navigating to `https://localhost` in your browser.
   - The default username is **admin**, and the password can be found in `<ASCENDER-INSTALL-SOURCE>/default.config.yml` under the `ASCENDER_ADMIN_PASSWORD` variable.

## Uninstall

1. **Delete Kubernetes Manifests**
   - After running `setup.sh`, `tmp_dir` (default: `{{ playbook_dir }}/../ascender_install_artifacts`) will contain timestamped Kubernetes manifests for:
     - `ascender-deployment-{{ k8s_platform }}.yml`
     - `ledger-{{ k8s_platform }}.yml` (if Ledger was installed)
     - `kustomization.yml`

2. **Remove the Timestamp from the Filename** and then run the following commands from within `tmp_dir`:
   ```bash
   kubectl delete -f ascender-deployment-{{ k8s_platform }}.yml
   kubectl delete -f ledger-{{ k8s_platform }}.yml  # optional if Ledger was installed
   kubectl delete -k .
   ```

Running the Ascender deletion steps will remove all related deployments and stateful sets. However, **persistent volumes and secrets** will remain. To enforce secret removal, use `ascender_garbage_collect_secrets: true` in the `default.config.yml` file.

## Conclusion

Using the **Ascender Installer** with **K3s** on **Rocky 9 or RHEL 9** provides an efficient way to set up a sandbox environment for testing the Ascender Automation Platform. This guide helps you configure Ascender for both online and offline environments, connect to its web UI, and uninstall it if necessary.

[Tutorial Here:](https://www.youtube.com/watch?v=lswN7Ct1cjE)
[Documentaton on K3S Here:](https://docs.k3s.io/quick-start)
[Documentation for Ascender here:](https://github.com/ctrliq/ascender-install/blob/main/docs/k3s/README.md#k3s-specific-prerequisites)
