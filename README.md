# PAM/PIV Smart Card Login Manager

This project integrates **PAM (Pluggable Authentication Modules)** with **PIV (Personal Identity Verification)** smart cards to provide secure login on Linux systems. It enables both **local** and **SSH-based authentication** using smart cards.

## Features
- Secure **smart card-based login** with PIV cards.
- Works with **SSH** for remote login.
- Supports **PIN caching** for smoother authentication.
- Configurable with **OpenSC** middleware.

## Setup

### 1. Install Dependencies
```bash
sudo apt update
sudo apt install opensc openssl pcscd pcsc-tools
sudo systemctl start pcscd
sudo systemctl enable pcscd

### 2. Configure PAM and SSH

#### Modify /etc/pam.d/common-auth
This ensures the system uses PAM for smart card login:
```bash
auth required pam_pkcs11.so
auth required pam_unix.so nullok_secure
```

#### Modify /etc/ssh/sshd_config
Enable public key and smart card authentication for SSH login:
```bash
PubkeyAuthentication yes
AuthorizedKeysFile /etc/ssh/%u/authorized_keys
```

### 3. Export the PIV Certificate
Use the following commands to export the PIV certificate and verify it:
```bash
pkcs11-tool --read-object --type cert --id 01 --output piv-cert.pem
openssl x509 -in piv-cert.pem -text -noout
```

### 4. Link the Certificate to a User
Link the PIV certificate to a user's SSH keys to enable smart card login:
```bash
mkdir -p /etc/ssh/<username>
cat piv-cert.pem >> /etc/ssh/<username>/authorized_keys
chmod 600 /etc/ssh/<username>/authorized_keys
```

### Troubleshooting

#### Check Logs for Errors
View logs to troubleshoot authentication issues:
```bash
sudo tail -f /var/log/auth.log
```

#### Ensure PC/SC Daemon is Running
Start the PC/SC daemon if it isn’t running:
```bash
sudo systemctl start pcscd
```
