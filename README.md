# lidarr Installation Guide

lidarr is a free and open-source music collection manager. Lidarr automates music downloading and organization, part of the *arr suite

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for app
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8686 (default lidarr port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install lidarr
sudo dnf install -y lidarr

# Enable and start service
sudo systemctl enable --now lidarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=8686/tcp
sudo firewall-cmd --reload

# Verify installation
lidarr --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install lidarr
sudo apt install -y lidarr

# Enable and start service
sudo systemctl enable --now lidarr

# Configure firewall
sudo ufw allow 8686

# Verify installation
lidarr --version
```

### Arch Linux

```bash
# Install lidarr
sudo pacman -S lidarr

# Enable and start service
sudo systemctl enable --now lidarr

# Verify installation
lidarr --version
```

### Alpine Linux

```bash
# Install lidarr
apk add --no-cache lidarr

# Enable and start service
rc-update add lidarr default
rc-service lidarr start

# Verify installation
lidarr --version
```

### openSUSE/SLES

```bash
# Install lidarr
sudo zypper install -y lidarr

# Enable and start service
sudo systemctl enable --now lidarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=8686/tcp
sudo firewall-cmd --reload

# Verify installation
lidarr --version
```

### macOS

```bash
# Using Homebrew
brew install lidarr

# Start service
brew services start lidarr

# Verify installation
lidarr --version
```

### FreeBSD

```bash
# Using pkg
pkg install lidarr

# Enable in rc.conf
echo 'lidarr_enable="YES"' >> /etc/rc.conf

# Start service
service lidarr start

# Verify installation
lidarr --version
```

### Windows

```bash
# Using Chocolatey
choco install lidarr

# Or using Scoop
scoop install lidarr

# Verify installation
lidarr --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/lidarr

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
lidarr --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable lidarr

# Start service
sudo systemctl start lidarr

# Stop service
sudo systemctl stop lidarr

# Restart service
sudo systemctl restart lidarr

# Check status
sudo systemctl status lidarr

# View logs
sudo journalctl -u lidarr -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add lidarr default

# Start service
rc-service lidarr start

# Stop service
rc-service lidarr stop

# Restart service
rc-service lidarr restart

# Check status
rc-service lidarr status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'lidarr_enable="YES"' >> /etc/rc.conf

# Start service
service lidarr start

# Stop service
service lidarr stop

# Restart service
service lidarr restart

# Check status
service lidarr status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start lidarr
brew services stop lidarr
brew services restart lidarr

# Check status
brew services list | grep lidarr
```

### Windows Service Manager

```powershell
# Start service
net start lidarr

# Stop service
net stop lidarr

# Using PowerShell
Start-Service lidarr
Stop-Service lidarr
Restart-Service lidarr

# Check status
Get-Service lidarr
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream lidarr_backend {
    server 127.0.0.1:8686;
}

server {
    listen 80;
    server_name lidarr.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name lidarr.example.com;

    ssl_certificate /etc/ssl/certs/lidarr.example.com.crt;
    ssl_certificate_key /etc/ssl/private/lidarr.example.com.key;

    location / {
        proxy_pass http://lidarr_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName lidarr.example.com
    Redirect permanent / https://lidarr.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName lidarr.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/lidarr.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/lidarr.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8686/
    ProxyPassReverse / http://127.0.0.1:8686/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend lidarr_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/lidarr.pem
    redirect scheme https if !{ ssl_fc }
    default_backend lidarr_backend

backend lidarr_backend
    balance roundrobin
    server lidarr1 127.0.0.1:8686 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R lidarr:lidarr /etc/lidarr
sudo chmod 750 /etc/lidarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=8686/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status lidarr

# View logs
sudo journalctl -u lidarr -f

# Monitor resource usage
top -p $(pgrep lidarr)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/lidarr"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/lidarr-backup-$DATE.tar.gz" /etc/lidarr /var/lib/lidarr

echo "Backup completed: $BACKUP_DIR/lidarr-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop lidarr

# Restore from backup
tar -xzf /backup/lidarr/lidarr-backup-*.tar.gz -C /

# Start service
sudo systemctl start lidarr
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u lidarr -n 100
sudo tail -f /var/log/lidarr/lidarr.log

# Check configuration
lidarr --version

# Check permissions
ls -la /etc/lidarr
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8686

# Test connectivity
telnet localhost 8686

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep lidarr)

# Check disk I/O
iotop -p $(pgrep lidarr)

# Check connections
ss -an | grep 8686
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  lidarr:
    image: lidarr:latest
    ports:
      - "8686:8686"
    volumes:
      - ./config:/etc/lidarr
      - ./data:/var/lib/lidarr
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update lidarr

# Debian/Ubuntu
sudo apt update && sudo apt upgrade lidarr

# Arch Linux
sudo pacman -Syu lidarr

# Alpine Linux
apk update && apk upgrade lidarr

# openSUSE
sudo zypper update lidarr

# FreeBSD
pkg update && pkg upgrade lidarr

# Always backup before updates
tar -czf /backup/lidarr-pre-update-$(date +%Y%m%d).tar.gz /etc/lidarr

# Restart after updates
sudo systemctl restart lidarr
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/lidarr

# Clean old logs
find /var/log/lidarr -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/lidarr
```

## Additional Resources

- Official Documentation: https://docs.lidarr.org/
- GitHub Repository: https://github.com/lidarr/lidarr
- Community Forum: https://forum.lidarr.org/
- Best Practices Guide: https://docs.lidarr.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
