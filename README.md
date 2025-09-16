# dolibarr Installation Guide

dolibarr is a free and open-source ERP/CRM system. Dolibarr provides ERP/CRM for small and medium businesses

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
  - RAM: 1GB minimum
  - Storage: 5GB for data
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default dolibarr port)
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

# Install dolibarr
sudo dnf install -y dolibarr

# Enable and start service
sudo systemctl enable --now dolibarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
dolibarr --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install dolibarr
sudo apt install -y dolibarr

# Enable and start service
sudo systemctl enable --now dolibarr

# Configure firewall
sudo ufw allow 80

# Verify installation
dolibarr --version
```

### Arch Linux

```bash
# Install dolibarr
sudo pacman -S dolibarr

# Enable and start service
sudo systemctl enable --now dolibarr

# Verify installation
dolibarr --version
```

### Alpine Linux

```bash
# Install dolibarr
apk add --no-cache dolibarr

# Enable and start service
rc-update add dolibarr default
rc-service dolibarr start

# Verify installation
dolibarr --version
```

### openSUSE/SLES

```bash
# Install dolibarr
sudo zypper install -y dolibarr

# Enable and start service
sudo systemctl enable --now dolibarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
dolibarr --version
```

### macOS

```bash
# Using Homebrew
brew install dolibarr

# Start service
brew services start dolibarr

# Verify installation
dolibarr --version
```

### FreeBSD

```bash
# Using pkg
pkg install dolibarr

# Enable in rc.conf
echo 'dolibarr_enable="YES"' >> /etc/rc.conf

# Start service
service dolibarr start

# Verify installation
dolibarr --version
```

### Windows

```bash
# Using Chocolatey
choco install dolibarr

# Or using Scoop
scoop install dolibarr

# Verify installation
dolibarr --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/dolibarr

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
dolibarr --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable dolibarr

# Start service
sudo systemctl start dolibarr

# Stop service
sudo systemctl stop dolibarr

# Restart service
sudo systemctl restart dolibarr

# Check status
sudo systemctl status dolibarr

# View logs
sudo journalctl -u dolibarr -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add dolibarr default

# Start service
rc-service dolibarr start

# Stop service
rc-service dolibarr stop

# Restart service
rc-service dolibarr restart

# Check status
rc-service dolibarr status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'dolibarr_enable="YES"' >> /etc/rc.conf

# Start service
service dolibarr start

# Stop service
service dolibarr stop

# Restart service
service dolibarr restart

# Check status
service dolibarr status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start dolibarr
brew services stop dolibarr
brew services restart dolibarr

# Check status
brew services list | grep dolibarr
```

### Windows Service Manager

```powershell
# Start service
net start dolibarr

# Stop service
net stop dolibarr

# Using PowerShell
Start-Service dolibarr
Stop-Service dolibarr
Restart-Service dolibarr

# Check status
Get-Service dolibarr
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream dolibarr_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name dolibarr.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name dolibarr.example.com;

    ssl_certificate /etc/ssl/certs/dolibarr.example.com.crt;
    ssl_certificate_key /etc/ssl/private/dolibarr.example.com.key;

    location / {
        proxy_pass http://dolibarr_backend;
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
    ServerName dolibarr.example.com
    Redirect permanent / https://dolibarr.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName dolibarr.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/dolibarr.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/dolibarr.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend dolibarr_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/dolibarr.pem
    redirect scheme https if !{ ssl_fc }
    default_backend dolibarr_backend

backend dolibarr_backend
    balance roundrobin
    server dolibarr1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R dolibarr:dolibarr /etc/dolibarr
sudo chmod 750 /etc/dolibarr

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
sudo systemctl status dolibarr

# View logs
sudo journalctl -u dolibarr -f

# Monitor resource usage
top -p $(pgrep dolibarr)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/dolibarr"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/dolibarr-backup-$DATE.tar.gz" /etc/dolibarr /var/lib/dolibarr

echo "Backup completed: $BACKUP_DIR/dolibarr-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop dolibarr

# Restore from backup
tar -xzf /backup/dolibarr/dolibarr-backup-*.tar.gz -C /

# Start service
sudo systemctl start dolibarr
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u dolibarr -n 100
sudo tail -f /var/log/dolibarr/dolibarr.log

# Check configuration
dolibarr --version

# Check permissions
ls -la /etc/dolibarr
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
top -p $(pgrep dolibarr)

# Check disk I/O
iotop -p $(pgrep dolibarr)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  dolibarr:
    image: dolibarr:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/dolibarr
      - ./data:/var/lib/dolibarr
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update dolibarr

# Debian/Ubuntu
sudo apt update && sudo apt upgrade dolibarr

# Arch Linux
sudo pacman -Syu dolibarr

# Alpine Linux
apk update && apk upgrade dolibarr

# openSUSE
sudo zypper update dolibarr

# FreeBSD
pkg update && pkg upgrade dolibarr

# Always backup before updates
tar -czf /backup/dolibarr-pre-update-$(date +%Y%m%d).tar.gz /etc/dolibarr

# Restart after updates
sudo systemctl restart dolibarr
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/dolibarr

# Clean old logs
find /var/log/dolibarr -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/dolibarr
```

## Additional Resources

- Official Documentation: https://docs.dolibarr.org/
- GitHub Repository: https://github.com/dolibarr/dolibarr
- Community Forum: https://forum.dolibarr.org/
- Best Practices Guide: https://docs.dolibarr.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
