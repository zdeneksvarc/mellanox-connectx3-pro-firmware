# ConnectX‑3 Pro (MT27520) — Firmware Check & Upgrade on Linux

This README walks you through checking the currently running and flashed firmware versions of a Mellanox/NVIDIA ConnectX‑3 Pro adapter on Linux, and upgrading to the latest official firmware.

> **Model example used below:** MT27520 Family [ConnectX‑3 Pro], e.g. MCX312B‑XCC

---

## Prerequisites

- Root access (use `sudo`)
- Tools:
  - `ethtool` (to query the running driver/firmware via netdev name)
  - `mstflint` (to query and burn firmware via PCI address)

On Debian/Ubuntu:

```bash
sudo apt update && sudo apt install ethtool mstflint
```

On RHEL/CentOS/Rocky/AlmaLinux:

```bash
sudo dnf install ethtool mstflint
```

---

## 1) Check the firmware version

### Option A — via network interface name (shows **running** firmware only)

```bash
sudo ethtool -i enp2s0
```

- This reports data for the given interface (replace `enp2s0` with your NIC name).
- **Note:** `ethtool` shows the firmware that is **currently running**.

### Option B — via PCI address (shows **flashed** and **running** firmware)

```bash
sudo mstflint -d 02:00.0 query
```

- Replace `02:00.0` with your adapter’s PCI address.
- `mstflint` reports both the firmware stored in the card **(on flash)** and the firmware **currently running**.

---

## 2) Latest official firmware

- The last official firmware release for ConnectX‑3 Pro is **v2.42.5000** from 2017
- Firmware download page: [https://network.nvidia.com/support/firmware/connectx3proe](https://network.nvidia.com/support/firmware/connectx3proe)
- Release Notes (PDF): [https://network.nvidia.com/pdf/firmware/ConnectX3Pro-FW-2\_42\_5000-release\_notes.pdf](https://network.nvidia.com/pdf/firmware/ConnectX3Pro-FW-2_42_5000-release_notes.pdf)

> Always verify that the image you flash matches your exact card part number (e.g., MCX312B‑XCC) and board revision.

---

## 3) Find the adapter’s PCI address

List PCI devices and locate the Mellanox/NVIDIA entry:

```bash
sudo lspci
```

Example output snippet:

```
02:00.0 Ethernet controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]
```

Here, the PCI address to use is ``.

---

## 4) Upgrade (burn) the firmware

Use the PCI address with `mstflint` and provide the correct firmware image (`.bin`) for your card:

```bash
sudo mstflint -d 02:00.0 -i fw-ConnectX3Pro-rel-2_42_5000-MCX312B-XCC_Ax-FlexBoot-3.4.752.bin burn
```

Example session:

```
Current FW version on flash:  2.36.5150
New FW version:               2.42.5000

Burning FS2 FW image without signatures - OK
Restoring signature                     - OK
```

> After burning, a reboot or driver reset may be required for the new firmware to run.

---

## Troubleshooting

### "write counter to semaphore: Operation not permitted"

If the burn fails with a semaphore/permission error, it can be caused by **UEFI Secure Boot** protections. A practical workaround is to **temporarily disable Secure Boot in UEFI**, perform the firmware update, and then re‑enable Secure Boot.

> Ensure you understand your platform’s security policy before disabling Secure Boot.

---

## Notes & Safety

- Flashing firmware carries risk. Make sure the image exactly matches your device SKU and revision.
- Perform updates during a maintenance window and keep physical/remote access in case of issues.
- Keep a copy of the current firmware details (`mstflint ... query`) for records before upgrading.

super, soubor ke stažení

