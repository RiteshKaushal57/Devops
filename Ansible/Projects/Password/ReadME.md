# Setting Up Passwordless SSH Authentication for Ansible

Ansible connects to remote servers over SSH. By default, it requires a password or key every time it runs. Setting up **passwordless authentication** allows Ansible to connect automatically without any manual input — which is essential for automation.

There are two ways to achieve this:
1. **Using a Public Key (PEM file)** — authenticate using your existing AWS key pair
2. **Using a Password** — set a password on the remote user, then copy your key over

---

## Method 1: Using a Public Key (PEM File)

This method uses your existing AWS `.pem` file to authenticate once and copy your local SSH public key to the remote server. After this, SSH (and Ansible) will use your local key directly — no PEM file needed.

### Step 1 — Copy PEM to WSL and fix permissions

> **Why?** PEM files stored on the Windows filesystem (`/mnt/c/...`) have overly open permissions (0777) which SSH rejects. Copying to WSL's own filesystem allows us to set the correct permissions.

```bash
cp /mnt/c/Users/<your-username>/Downloads/AWS.pem ~/.ssh/AWS.pem
chmod 400 ~/.ssh/AWS.pem
```

### Step 2 — Copy your public key to the remote server

> **Why?** `ssh-copy-id` appends your local public key (`~/.ssh/id_ed25519.pub`) to the remote server's `~/.ssh/authorized_keys`. The `-f` flag forces the copy, and `-o IdentityFile` tells SSH to use the PEM file for this one-time login.

```bash
ssh-copy-id -f "-o IdentityFile ~/.ssh/AWS.pem" ubuntu@<INSTANCE-PUBLIC-IP>
```

**Expected output:**
```
Number of key(s) added: 1
Now try logging into the machine with: "ssh 'ubuntu@<INSTANCE-PUBLIC-IP>'"
```

### Step 3 — Verify passwordless login

```bash
ssh ubuntu@<INSTANCE-PUBLIC-IP>
```

You should be logged in without being asked for a password or PEM file. ✅

---

## Method 2: Using a Password

This method is useful when you don't want to rely on a PEM file at all. You enable password authentication on the server, set a password, and then use `ssh-copy-id` to copy your key — which will prompt for the password once.

### Step 1 — SSH into the instance using the PEM file

> **Why?** We need to get inside the instance first to enable password authentication, which is disabled by default on AWS Ubuntu instances.

```bash
# Copy PEM to WSL and fix permissions (if not done already)
cp /mnt/c/Users/<your-username>/Downloads/AWS.pem ~/.ssh/AWS.pem
chmod 400 ~/.ssh/AWS.pem

# SSH into the instance
ssh -i ~/.ssh/AWS.pem ubuntu@<INSTANCE-PUBLIC-IP>
```

### Step 2 — Enable password authentication on the instance

> **Why?** AWS Ubuntu instances disable password authentication by default for security. We need to enable it so `ssh-copy-id` can log in using a password in the next step.

```bash
sudo vim /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
```

Find the line `PasswordAuthentication no` and change it to:
```
PasswordAuthentication yes
```

### Step 3 — Restart SSH and set a password

> **Why?** The SSH service must be restarted for the config change to take effect. Then we set a password for the `ubuntu` user.

```bash
sudo systemctl restart ssh
sudo passwd ubuntu
```

### Step 4 — Logout and copy your public key

> **Why?** Now that password authentication is enabled, `ssh-copy-id` can log in using the password and copy your public key. After this, future logins will use the key — no password needed.

```bash
logout
ssh-copy-id ubuntu@<INSTANCE-PUBLIC-IP>
```

When prompted, enter the password you just set. **Expected output:**
```
Number of key(s) added: 1
```

### Step 5 — Verify passwordless login

```bash
ssh ubuntu@<INSTANCE-PUBLIC-IP>
```

You should be logged in without being asked for a password. ✅

---

## Using with Ansible

Once passwordless authentication is set up, create an inventory file:

```ini
# inventory.ini
[webservers]
<INSTANCE-PUBLIC-IP> ansible_user=ubuntu
```

Test the connection:
```bash
ansible -i inventory.ini webservers -m ping
```

**Expected output:**
```json
<INSTANCE-PUBLIC-IP> | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Ansible is now ready to manage your instances without any manual authentication. 🎉