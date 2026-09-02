# Commands — Passwordless SSH Authentication

This document contains the commands used to configure and verify **passwordless SSH authentication between two Linux/EC2 servers**.

## Lab Environment

| Server   | Role                   | Private IP      |
| -------- | ---------------------- | --------------- |
| Server A | Source / Initiator     | `172.31.31.174` |
| Server B | Destination / Receiver | `172.31.22.11`  |

> Replace the IP addresses and usernames with those of your own EC2 instances when reproducing the setup.

---

# 1. Connect to Server A

SSH into the source server:

```bash
ssh <username>@172.31.31.174
```

Server A will initiate the SSH connection to Server B.

---

# 2. Generate SSH Key Pair

Generate an RSA key pair on Server A:

```bash
ssh-keygen -t rsa
```

Press `Enter` to use the default location when prompted.

This generates:

```text
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

Where:

* `id_rsa` → Private key
* `id_rsa.pub` → Public key

---

# 3. Verify Generated Keys

List the contents of the `.ssh` directory:

```bash
ls -la ~/.ssh
```

Expected key files:

```text
id_rsa
id_rsa.pub
```

---

# 4. Display the Public Key

Display the public key:

```bash
cat ~/.ssh/id_rsa.pub
```

Copy the complete output.

> **Important:** Only copy the public key. Never share the private key `id_rsa`.

---

# 5. Connect to Server B

From Server A, connect to Server B:

```bash
ssh <username>@172.31.22.11
```

---

# 6. Create the `.ssh` Directory

On Server B, create the `.ssh` directory if it does not already exist:

```bash
mkdir -p ~/.ssh
```

---

# 7. Configure `authorized_keys`

Open the `authorized_keys` file:

```bash
nano ~/.ssh/authorized_keys
```

Paste the public key copied from Server A into this file.

The public key should be stored at:

```text
~/.ssh/authorized_keys
```

---

# 8. Set SSH Directory Permissions

Set the required permissions for the `.ssh` directory:

```bash
chmod 700 ~/.ssh
```

---

# 9. Set `authorized_keys` Permissions

Set the required permissions for the `authorized_keys` file:

```bash
chmod 600 ~/.ssh/authorized_keys
```

The resulting permissions should be:

```text
~/.ssh                  → 700
~/.ssh/authorized_keys  → 600
```

---

# 10. Test Passwordless SSH

Return to Server A and connect to Server B:

```bash
ssh <username>@172.31.22.11
```

If the public-key authentication has been configured correctly, the connection should be established **without requiring the account password**.

---

# 11. Verify SSH Authentication

Use verbose SSH mode to inspect the authentication process:

```bash
ssh -v <username>@172.31.22.11
```

Look for messages indicating that SSH is offering the public key:

```text
Offering public key
```

A successful authentication should contain a message similar to:

```text
Authentication succeeded
```

---

# 12. Complete Command Sequence

For quick reference, the main commands used in the setup are:

### On Server A

```bash
ssh <username>@172.31.31.174
```

```bash
ssh-keygen -t rsa
```

```bash
ls -la ~/.ssh
```

```bash
cat ~/.ssh/id_rsa.pub
```

### On Server B

```bash
ssh <username>@172.31.22.11
```

```bash
mkdir -p ~/.ssh
```

```bash
nano ~/.ssh/authorized_keys
```

```bash
chmod 700 ~/.ssh
```

```bash
chmod 600 ~/.ssh/authorized_keys
```

### Back on Server A

```bash
ssh <username>@172.31.22.11
```

For troubleshooting:

```bash
ssh -v <username>@172.31.22.11
```

---

# ⚠️ Important Security Notes

* Never share the private key:

```text
~/.ssh/id_rsa
```

* Only the public key should be transferred to Server B:

```text
~/.ssh/id_rsa.pub
```

* The public key is stored on Server B in:

```text
~/.ssh/authorized_keys
```

* Ensure the `.ssh` directory and `authorized_keys` have the appropriate permissions.

* SSH communication requires **port 22** to be permitted between the servers through the relevant AWS EC2 Security Group configuration.

---

# ✅ Expected Result

After completing the configuration, Server A should be able to connect to Server B using SSH public-key authentication:

```text
Server A
   │
   │ ssh <username>@Server-B
   ▼
Server B
   │
   ▼
Public Key Authentication
   │
   ▼
Authentication Successful
   │
   ▼
SSH Session
```

**Result:** Server A can establish an SSH connection with Server B without manually entering the password.
