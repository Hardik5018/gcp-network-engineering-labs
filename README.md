# Lab 03 – Ubuntu Linux VM deployment and initial verification

## Objective
Deploy a small private Ubuntu Compute Engine VM in the custom VPC subnet, connect using SSH-in-browser, and validate its operating system and network configuration.

## VM configuration

| Setting | Value |
|---|---|
| VM name | `hp-linux-vm-01` |
| Region / zone | `asia-south1` / `asia-south1-a` |
| Machine type | `e2-micro` |
| OS image | Ubuntu 24.04 LTS (x86-64) |
| Boot disk | 10 GB standard persistent disk |
| VPC | `hp-network-lab` |
| Subnet | `hp-app-subnet` (`10.10.1.0/24`) |
| Network tag | `lab-vm` |
| IP stack | IPv4 single-stack |
| Internal address | Ephemeral (automatic); observed `10.10.1.2` |
| External IPv4 | None |
| IP forwarding | Disabled |
| HTTP / HTTPS checkboxes | Unchecked |

## Console procedure

1. Navigate to **Compute Engine → VM instances → Create instance**.
2. Name the VM `hp-linux-vm-01` and choose zone `asia-south1-a`.
3. Select machine type `e2-micro`.
4. Under OS and storage, select Ubuntu 24.04 LTS, x86-64, a 10 GB standard persistent disk.
5. Under Networking, retain one VPC network interface.
6. Select VPC `hp-network-lab` and subnet `hp-app-subnet`.
7. Select **No external IPv4 address**.
8. Set network tag `lab-vm` so the two custom firewall rules target the VM.
9. Keep IP forwarding disabled and leave HTTP/HTTPS firewall checkboxes unchecked.
10. Review and create the instance.
11. From VM instances, click **SSH** to open SSH-in-browser.

## Initial Linux verification

Run these read-only commands after SSH login:

| Command | Purpose | Observed result |
|---|---|---|
| `hostname` | Show host name | `hp-linux-vm-01` |
| `hostnamectl` | OS, kernel, virtualization and architecture | Ubuntu 24.04.5 LTS; x86-64; Google Compute Engine |
| `uname -a` | Kernel and machine details | Linux 7.0.0-1014-gcp shown in session |
| `ip addr` | Network interface and IP | `ens4` UP; `10.10.1.2/32` |
| `ip route` | Kernel routing table | Default route via `10.10.1.1` on `ens4` |
| `free -h` | Memory summary | About 953 MiB total RAM shown |
| `df -h` | Filesystem capacity and usage | Root filesystem about 8.7 GB; 28% used in session |

### Why does Linux show `/32`?

The guest interface showed `10.10.1.2/32`. This is expected for Compute Engine VPC network interfaces; Google Cloud uses a host-route model inside the guest rather than configuring the whole subnet mask on the VM NIC.

## Evidence

- ![VM instance](screenshots/04-vm-instance.png)
- ![Linux verification output](screenshots/05-linux-verification.png)

## Result and current state

SSH-in-browser opened successfully and the Linux verification commands returned output. The VM was subsequently stopped after the practice session. The internal address recorded during the session was `10.10.1.2`; an ephemeral internal IP can change when the VM is stopped and started again, so verify it in the console before relying on it.

## Pending follow-up

The Compute Engine metadata server exercise was proposed but the commands have not yet been run. Once the VM is started again, run:

```bash
curl -H "Metadata-Flavor: Google" \
  http://metadata.google.internal/computeMetadata/v1/instance/name

curl -H "Metadata-Flavor: Google" \
  http://metadata.google.internal/computeMetadata/v1/instance/zone

curl -H "Metadata-Flavor: Google" \
  http://metadata.google.internal/computeMetadata/v1/instance/network-interfaces/0/ip

curl -H "Metadata-Flavor: Google" \
  http://metadata.google.internal/computeMetadata/v1/instance/tags
```

These are next-step commands, not completed results.
