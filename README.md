# Talos Anywhere

A "Zero-Touch" Ansible playbook to bootstrap and secure a Talos Linux cluster on any bare metal or server provider (OVH, Hetzner, DigitalOcean, etc.) using `kexec` to replace the running OS in-memory.

## Features
-   **Provider Agnostic**: Works on any server that supports `ssh` and `kexec`.
-   **Zero-Touch**: Automatically detects public IP/Gateway from the running OS.
-   **Secure**: Installs standard Talos image, then immediately upgrades to a verified image with extensions (Tailscale, etc.). Firewall rules are **automatically applied** when Tailscale is enabled, blocking all generic traffic (port 50000/6443) except from the Tailscale subnet.
-   **Tailscale Integration**: Automatically joins the cluster to Tailscale and configures local `talosconfig` to use the Tailscale IP.

## Prerequisites
-   **Ansible**: Installed locally.
-   **Server**: A running Linux server (Ubuntu/Debian/CentOS) with SSH access.
-   **Tailscale Key**: An auth key for joining the mesh. *Optional*

## Quick Start

1.  **Configure Cluster**:
    Edit `vars/cluster.yml`:
    ```yaml
    talos_version: "v1.12.0"
    tailscale_auth_key: "tskey-auth-..."
    talos_install_disk: "/dev/sda"  # Check your server disk name!
    ```

2.  **Define Inventory**:
    Edit `inventory.ini` with your server IP and SSH user:
    ```ini
    [server_cp]
    1.2.3.4 ansible_user=root
    ```

3.  **Run Playbook**:
    ```bash
    # If using SSH Keys (Recommended):
    ansible-playbook -i inventory.ini site.yml

    # If using Passwords (SSH + Sudo):
    ansible-playbook -i inventory.ini site.yml -k -K
    ```

## Post-Install
After the playbook completes:
-   **Access**: The public IP (port 50000) will be closed.
-   **Manage**: Use `talosconfig` with the automatically discovered Tailscale IP.
    ```bash
    talosctl dashboard --talosconfig talos_cluster/talosconfig
    ```
