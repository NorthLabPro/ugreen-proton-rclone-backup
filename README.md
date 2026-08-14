# UGREEN NAS → Proton Drive Backup

A practical, tested guide for backing up files from a **UGREEN NAS running UGOS Pro** to **Proton Drive** using **Docker and rclone**.

This project documents a real working setup, including the problems encountered during configuration and the solutions that worked.

> [!IMPORTANT]
> This guide currently uses **rclone 1.74.4**. During testing, newer versions caused problems with files being uploaded to Proton Drive but not opening correctly. Always test with a small number of files before starting a large backup.

## Tested Setup

- **NAS:** UGREEN DXP4800 Plus
- **Operating system:** UGOS Pro
- **Container system:** Docker / Docker Projects
- **Cloud storage:** Proton Drive / Proton Unlimited
- **Transfer software:** rclone 1.74.4
- **Authentication:** Proton 2FA
- **Backup method:** `rclone copy`

The setup was tested with a real photo archive containing **more than 24,000 files and over 322 GiB of data**.

## Why this project exists

UGREEN currently does not provide a built-in Proton Drive backup option.

Proton Drive also does not provide an official NAS client for UGOS Pro.

The solution used here is:

```text
UGREEN NAS
    │
    ▼
Docker
    │
    ▼
rclone
    │
    ▼
Proton Drive
```

The NAS remains the **primary storage**.

Proton Drive is used as an **off-site backup**, rather than making the cloud the primary copy of the data.

## What this guide covers

This repository will explain:

1. Creating the Docker project in UGOS Pro
2. Installing the correct rclone Docker image
3. Mapping NAS folders into the container
4. Connecting rclone to Proton Drive
5. Configuring Proton two-factor authentication
6. Using the Proton **Base32 OTP Secret Key**
7. Removing an expired temporary `2fa` value from `rclone.conf`
8. Testing the connection
9. Uploading a single test file
10. Running a full backup
11. Resuming after power or network interruption
12. Backing up photos and videos separately
13. Troubleshooting Proton Drive errors
14. Dealing with `422`, `not found`, and `already exists` errors
15. Choosing safe `transfers`, `checkers`, retry and logging settings
16. Automating the backup after the configuration has been verified

## Important: `copy` vs `sync`

This guide uses:

```bash
rclone copy
```

and **not**:

```bash
rclone sync
```

For an archive containing family photos or other important files, `copy` is the safer starting point.

Files that already exist at the destination can be skipped, while deleting a file from the NAS does not automatically mean that the cloud backup should also be deleted.


Example configuration files in this repository will use placeholders instead of real credentials.

## Repository structure

```text
ugreen-proton-rclone-backup/
├── README.md
├── LICENSE
├── docker-compose.yml
├── docs/
│   ├── setup.md
│   ├── proton-2fa.md
│   └── troubleshooting.md
└── images/
```

## Status

🚧 **Documentation in progress**

The setup itself has already been tested successfully. The repository is being built from the working configuration and the troubleshooting notes collected during the original installation.

---

Created and maintained by **NorthLabPro**.
