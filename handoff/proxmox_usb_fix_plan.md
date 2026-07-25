# Plan: Resolve Proxmox USB Passthrough Conflict

We have discovered the root cause of the UAT USB failure.

There are **three** RTL-SDR dongles connected to the Proxmox host:
1. `1-4` (`0bda:2838` / serial `1090`) — Primary ADS-B SDR.
2. `1-3` (`0bda:2832` / serial `1090`) — Generic SDR used by Home Assistant.
3. `1-9` (`0bda:2832` / serial `978`) — New ADSBexchange orange SDR for UAT.

### The Problem
* **VM 100 (Home Assistant)** is configured to claim `host=0bda:2832` without a serial number.
* **VM 102 (ADS-B VM)** is configured to claim `host=0bda:2832` without a serial number.
* Because both VMs are claiming the same generic device ID, QEMU has opened `/dev/bus/usb/001/060` (the new `978` SDR) in **both VMs simultaneously**.
* This conflict causes the host kernel to reject the VM's bulk-data requests, resulting in the `Library error -5` and `cb transfer status: 1` crashes. Meanwhile, the older `1090` SDR (`1-3`) is sitting completely idle and unused.

---

## Proposed Changes

We will update the Proxmox VM configurations on the hypervisor (`10.10.1.50`) to explicitly use serial numbers:

### 1. Update VM 100 (Home Assistant)
Configure it to explicitly target the `1090` generic SDR:
```diff
# /etc/pve/qemu-server/100.conf
- usb0: host=0bda:2832
+ usb0: host=0bda:2832,serial=1090
```

### 2. Update VM 102 (ADS-B VM)
Configure it to explicitly target the new `978` SDR for its secondary device:
```diff
# /etc/pve/qemu-server/102.conf
- usb1: host=0bda:2832
+ usb1: host=0bda:2832,serial=978
```

---

## Deployment & Restart Steps

1. **Apply Config Updates**:
   Edit `/etc/pve/qemu-server/100.conf` and `/etc/pve/qemu-server/102.conf` on the Proxmox host.
2. **Reload USB configurations**:
   To apply the USB changes cleanly, we will stop and start the USB passthroughs, or restart the VMs.
   * We will restart VM 102 to let it cleanly claim the correct device.
   * We will also reboot VM 100 (Home Assistant) to release the `978` dongle and bind to the `1090` dongle.
3. **Verify**:
   * Confirm that VM 102 sees only the correct SDRs.
   * Run a quick `rtl_test -d 0` in VM 102 to verify that the stream is stable and no longer throws `Library error -5`.
   * Start `dump978` back up!
