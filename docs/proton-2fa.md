# Proton 2FA Setup for rclone

This section explains one of the most confusing parts of connecting **rclone to Proton Drive**: Proton's two-factor authentication (2FA).

There are **three different authentication values**, and they must not be confused.

---

## 1. Proton Account Password

This is your normal Proton account password.

During `rclone config`, enter it when rclone asks for your Proton password.

---

## 2. The 6-Digit 2FA Code

If 2FA is enabled on your Proton account, your authenticator generates a temporary six-digit code.

Example:

```text
123456
```

This code changes regularly.

It is used during the initial login, but it **cannot be used as the permanent OTP Secret Key**.

---

## 3. The OTP Secret Key

The OTP Secret Key is the long secret used by your authenticator to generate the changing six-digit codes.

It normally looks similar to this:

```text
JBSWY3DPEHPK3PXPXXXXXXXXXXXXXXXX
```

The example above is fake.

> [!CAUTION]
> **Never publish your real OTP Secret Key.**
>
> Anyone with your password and OTP Secret Key could potentially generate valid 2FA codes.

---

## Finding the OTP Secret Key

When setting up an authenticator manually, Proton provides the secret used to configure the authenticator.

You need the **Base32 secret**, not the current six-digit authentication code.

If your authenticator offers an option similar to:

```text
Add account manually
```

the value used as the secret/key is the value rclone needs for `otp_secret_key`.

> [!IMPORTANT]
> Do not disable or reset your existing Proton 2FA just to follow this guide unless you understand the consequences and have your recovery methods available.

---

## Configure rclone

Inside the Docker container, run:

```bash
rclone config
```

Create the Proton Drive remote and continue through the login process.

When rclone reaches:

```text
Option otp_secret_key
```

choose the option that allows you to enter your own value.

You may then see:

```text
Enter the password:
```

### This is the confusing part

At this point, **do not enter your normal Proton password again**.

Enter your:

**Base32 OTP Secret Key**

When rclone asks:

```text
Confirm the password:
```

enter the **same OTP Secret Key again**.

---

## Why does rclone call it a password?

The prompt is generic because rclone treats the OTP Secret Key as sensitive information.

So:

```text
Enter the password:
```

does not necessarily mean:

```text
Enter your Proton account password:
```

in this part of the configuration.

This caused considerable confusion during the original setup.

---

## Temporary `2fa` Value

After configuration, check the setup:

```bash
rclone config show
```

A temporary six-digit 2FA value should not remain permanently as:

```text
2fa =
```

The six-digit code expires.

If an old `2fa =` entry remains, it can be removed with:

```bash
sed -i '/^2fa =/d' /config/rclone/rclone.conf
```

Then check again:

```bash
rclone config show
```

> [!CAUTION]
> Do not copy the complete output of `rclone config show` into GitHub, Reddit, forums or support messages without removing credentials and secrets.

---

## Test the Authentication

After configuration, run:

```bash
rclone lsd protondrive:
```

If everything is working, rclone should list the folders in your Proton Drive.

Only after this works should you test an upload.

---

## Quick Reference

| rclone asks for | What to enter |
|---|---|
| Proton username | Your Proton account email |
| Proton password | Your normal Proton password |
| 6-digit 2FA | Current code from your authenticator |
| `otp_secret_key` | Your long Base32 OTP Secret Key |
| Confirm OTP secret | The same Base32 key again |

---

## Security

Never store these in this GitHub repository:

- Proton password
- OTP Secret Key
- 2FA recovery codes
- Proton recovery information
- A real `rclone.conf`

The repository should contain **instructions only**, never real authentication credentials.

---

[← Setup Guide](setup.md) | [Troubleshooting](troubleshooting.md) | [Main README](../README.md)
