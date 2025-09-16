# suitecrm Installation Guide

suitecrm is a free and open-source customer relationship management. SuiteCRM provides enterprise-ready CRM as SugarCRM fork

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
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 5GB for data
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default suitecrm port)
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

# Install suitecrm
sudo dnf install -y suitecrm

# Enable and start service
sudo systemctl enable --now suitecrm

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
suitecrm --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install suitecrm
sudo apt install -y suitecrm

# Enable and start service
sudo systemctl enable --now suitecrm

# Configure firewall
sudo ufw allow 80

# Verify installation
suitecrm --version
```

### Arch Linux

```bash
# Install suitecrm
sudo pacman -S suitecrm

# Enable and start service
sudo systemctl enable --now suitecrm

# Verify installation
suitecrm --version
```

### Alpine Linux

```bash
# Install suitecrm
apk add --no-cache suitecrm

# Enable and start service
rc-update add suitecrm default
rc-service suitecrm start

# Verify installation
suitecrm --version
```

### openSUSE/SLES

```bash
# Install suitecrm
sudo zypper install -y suitecrm

# Enable and start service
sudo systemctl enable --now suitecrm

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
suitecrm --version
```

### macOS

```bash
# Using Homebrew
brew install suitecrm

# Start service
brew services start suitecrm

# Verify installation
suitecrm --version
```

### FreeBSD

```bash
# Using pkg
pkg install suitecrm

# Enable in rc.conf
echo 'suitecrm_enable="YES"' >> /etc/rc.conf

# Start service
service suitecrm start

# Verify installation
suitecrm --version
```

### Windows

```bash
# Using Chocolatey
choco install suitecrm

# Or using Scoop
scoop install suitecrm

# Verify installation
suitecrm --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/suitecrm

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
suitecrm --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable suitecrm

# Start service
sudo systemctl start suitecrm

# Stop service
sudo systemctl stop suitecrm

# Restart service
sudo systemctl restart suitecrm

# Check status
sudo systemctl status suitecrm

# View logs
sudo journalctl -u suitecrm -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add suitecrm default

# Start service
rc-service suitecrm start

# Stop service
rc-service suitecrm stop

# Restart service
rc-service suitecrm restart

# Check status
rc-service suitecrm status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'suitecrm_enable="YES"' >> /etc/rc.conf

# Start service
service suitecrm start

# Stop service
service suitecrm stop

# Restart service
service suitecrm restart

# Check status
service suitecrm status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start suitecrm
brew services stop suitecrm
brew services restart suitecrm

# Check status
brew services list | grep suitecrm
```

### Windows Service Manager

```powershell
# Start service
net start suitecrm

# Stop service
net stop suitecrm

# Using PowerShell
Start-Service suitecrm
Stop-Service suitecrm
Restart-Service suitecrm

# Check status
Get-Service suitecrm
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream suitecrm_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name suitecrm.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name suitecrm.example.com;

    ssl_certificate /etc/ssl/certs/suitecrm.example.com.crt;
    ssl_certificate_key /etc/ssl/private/suitecrm.example.com.key;

    location / {
        proxy_pass http://suitecrm_backend;
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
    ServerName suitecrm.example.com
    Redirect permanent / https://suitecrm.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName suitecrm.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/suitecrm.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/suitecrm.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend suitecrm_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/suitecrm.pem
    redirect scheme https if !{ ssl_fc }
    default_backend suitecrm_backend

backend suitecrm_backend
    balance roundrobin
    server suitecrm1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R suitecrm:suitecrm /etc/suitecrm
sudo chmod 750 /etc/suitecrm

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status suitecrm

# View logs
sudo journalctl -u suitecrm -f

# Monitor resource usage
top -p $(pgrep suitecrm)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/suitecrm"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/suitecrm-backup-$DATE.tar.gz" /etc/suitecrm /var/lib/suitecrm

echo "Backup completed: $BACKUP_DIR/suitecrm-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop suitecrm

# Restore from backup
tar -xzf /backup/suitecrm/suitecrm-backup-*.tar.gz -C /

# Start service
sudo systemctl start suitecrm
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u suitecrm -n 100
sudo tail -f /var/log/suitecrm/suitecrm.log

# Check configuration
suitecrm --version

# Check permissions
ls -la /etc/suitecrm
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep suitecrm)

# Check disk I/O
iotop -p $(pgrep suitecrm)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  suitecrm:
    image: suitecrm:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/suitecrm
      - ./data:/var/lib/suitecrm
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update suitecrm

# Debian/Ubuntu
sudo apt update && sudo apt upgrade suitecrm

# Arch Linux
sudo pacman -Syu suitecrm

# Alpine Linux
apk update && apk upgrade suitecrm

# openSUSE
sudo zypper update suitecrm

# FreeBSD
pkg update && pkg upgrade suitecrm

# Always backup before updates
tar -czf /backup/suitecrm-pre-update-$(date +%Y%m%d).tar.gz /etc/suitecrm

# Restart after updates
sudo systemctl restart suitecrm
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/suitecrm

# Clean old logs
find /var/log/suitecrm -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/suitecrm
```

## Additional Resources

- Official Documentation: https://docs.suitecrm.org/
- GitHub Repository: https://github.com/suitecrm/suitecrm
- Community Forum: https://forum.suitecrm.org/
- Best Practices Guide: https://docs.suitecrm.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
