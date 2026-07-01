# SSH
# SSH – 2 Ways to Access a Remote Server

SSH (Secure Shell) is used to establish a secure, encrypted connection between two devices and allows remote login safely.

- Default port: `22` (can be changed, recommended range: `1024-32767`)

---

## Prerequisites

**Client (the machine you connect FROM)**
```bash
sudo apt install openssh-client
```

**Remote (the machine you connect TO)**
```bash
sudo apt install openssh-server
sudo systemctl status ssh
```

**Enable SSH (if not running)**
```bash
sudo systemctl enable ssh --now
```

**Find remote server's IP**
```bash
ip a   # usually visible under eth0
```

**Check if port 22 is listening**
```bash
ss -utlnp
```

**Firewall (may block access)**
```bash
systemctl status firewalld     # RHEL
systemctl stop firewalld

sudo ufw allow 22               # Kali (UFW = Uncomplicated Firewall)
sudo ufw status

# Or open a custom port via firewalld
firewall-cmd --list-all
firewall-cmd --permanent --add-port=2222/tcp
firewall-cmd --reload
```

> By default, root login is restricted — use a regular username instead.
> To allow root: edit `/etc/ssh/sshd_config` → set `PermitRootLogin yes` (optionally add a custom port).

---

## Way 1: Using Password

Connect directly using the remote user's password.

```bash
ssh username@host   # host = IP or domain name
```

**Example**
```bash
ssh kali@192.168.0.105
```

---

## Way 2: Without Password (SSH Key)

More secure — no password prompt after setup.

**1. Generate a key pair on the client**
```bash
ssh-keygen
```
This creates two files: `id_rsa` (private key) and `id_rsa.pub` (public key).

Custom example:
```bash
ssh-keygen -C "gurung@bishalgurung.com.np" -f "bishalgurungssh"
```

**2. Copy the public key to the remote server**
```bash
ssh-copy-id user@ip
```
Example:
```bash
cd ~/.ssh/
ssh-copy-id kali@192.168.0.105
```
This adds your key to `~/.ssh/authorized_keys` on the remote server.

**3. Login without a password**
```bash
ssh user@ip
```
Or, using a custom key location (`-i` = specify key path):
```bash
ssh -i <PrivateKeyLocation> <user>@<ip>
```
Example:
```bash
ssh -i bishalgurungssh kali@192.168.0.165
```

---

## Quick Reference

| Command | Purpose |
|---|---|
| `sudo apt install openssh-server` | Install SSH server |
| `sudo systemctl status ssh` | Check SSH status |
| `sudo systemctl enable ssh --now` | Enable & start SSH |
| `ip a` | Find server IP |
| `ss -utlnp` | Check listening ports |
| `ssh-keygen` | Generate key pair |
| `ssh-copy-id user@ip` | Copy public key to server |
| `ssh -i <key> user@ip` | Login using a specific private key |
