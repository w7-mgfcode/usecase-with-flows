# Deployment Guide

## Pre-Deployment Checklist

Complete these items before starting:

- [ ] System requirements met (see README.md)
- [ ] All environment variables defined
- [ ] Database backups created (if applicable)
- [ ] SSL certificates prepared
- [ ] Team notified of deployment window
- [ ] Rollback plan documented
- [ ] Monitoring configured

⚠️ **CRITICAL:** Do not proceed until ALL items are checked.

---

## Environment Setup

### Development Environment

```bash
# Clone and setup
git clone [repo-url]
cd [project-name]
npm install

# Configure
cp .env.example .env
# Edit .env with dev values
nano .env

# Verify setup
npm run verify
# Should see: All checks passed
```

### Production Environment

✅ **REQUIRED STEPS:**

#### 1. Server Preparation
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install dependencies
sudo apt install -y nodejs npm postgresql

# Create application user
sudo useradd -m -s /bin/bash appuser
```

#### 2. SSL Certificate
```bash
# If using Let's Encrypt
sudo certbot certonly --standalone -d yourdomain.com

# Store certificate path in environment
export SSL_CERT=/etc/letsencrypt/live/yourdomain.com/fullchain.pem
export SSL_KEY=/etc/letsencrypt/live/yourdomain.com/privkey.pem
```

#### 3. Database Initialization
```bash
sudo -u postgres psql << EOF
CREATE DATABASE appdb;
CREATE USER appuser WITH PASSWORD '[secure-password]';
ALTER ROLE appuser SET client_encoding TO 'utf8';
GRANT ALL PRIVILEGES ON DATABASE appdb TO appuser;
EOF
```

---

## Installation Steps

### Step 1: Deploy Application

```bash
# As appuser
cd /opt/[project-name]
git clone [repo-url] .
npm ci --production

# Compile/build if needed
npm run build
```

### Step 2: Configure Environment

Create `/opt/[project-name]/.env`:

```env
NODE_ENV=production
PORT=3000
DATABASE_URL=postgresql://appuser:PASSWORD@localhost/appdb
API_KEY=[production-key]
SSL_CERT=/etc/letsencrypt/live/yourdomain.com/fullchain.pem
SSL_KEY=/etc/letsencrypt/live/yourdomain.com/privkey.pem
```

⚠️ **SECURITY:** Never commit .env to version control

### Step 3: Set Permissions

```bash
sudo chown -R appuser:appuser /opt/[project-name]
sudo chmod 750 /opt/[project-name]
sudo chmod 600 /opt/[project-name]/.env
```

### Step 4: Configure Process Manager (systemd)

Create `/etc/systemd/system/[project-name].service`:

```ini
[Unit]
Description=[Project Name] Service
After=network.target

[Service]
User=appuser
WorkingDirectory=/opt/[project-name]
ExecStart=/usr/bin/node /opt/[project-name]/src/index.js
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Enable and start:
```bash
sudo systemctl daemon-reload
sudo systemctl enable [project-name]
sudo systemctl start [project-name]
```

### Step 5: Configure Reverse Proxy (Nginx)

Create `/etc/nginx/sites-available/[project-name]`:

```nginx
server {
    listen 80;
    server_name yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Enable:
```bash
sudo ln -s /etc/nginx/sites-available/[project-name] /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## Post-Deployment Verification

### Immediate Checks (5 minutes)

- [ ] Service is running: `sudo systemctl status [project-name]`
- [ ] Listening on correct port: `sudo netstat -tlnp | grep 3000`
- [ ] Health check passes: `curl -X GET http://localhost:3000/health`
- [ ] Database connected: Check logs for connection string
- [ ] No error messages in logs: `journalctl -u [project-name] -n 50`

### Functional Checks (15 minutes)

- [ ] Homepage loads: Visit https://yourdomain.com
- [ ] API endpoints respond: Test 3 critical endpoints
- [ ] Database queries work: Check application logs
- [ ] Authentication works (if applicable)
- [ ] Can handle at least 10 concurrent connections

### Monitoring Setup (30 minutes)

```bash
# Configure monitoring
sudo apt install prometheus node_exporter

# Add to crontab for monitoring
crontab -e
# Add: */5 * * * * /usr/local/bin/check-health.sh
```

---

## Rollback Procedure

If deployment fails:

### Immediate Rollback (< 1 minute)

```bash
# Stop current version
sudo systemctl stop [project-name]

# Switch back to previous version
cd /opt/[project-name]
git checkout previous-tag
npm ci --production
npm run build

# Restart
sudo systemctl start [project-name]

# Verify
curl http://localhost:3000/health
```

### Database Rollback

```bash
# Restore from backup
pg_restore -d appdb backup_YYYYMMDD.dump
```

### If Still Failing

1. Restore from snapshot if available
2. Contact DevOps team immediately
3. Do post-mortem within 24 hours

---

## Monitoring & Maintenance

### Daily Checks

- Service health: `sudo systemctl status [project-name]`
- Disk space: `df -h`
- Memory: `free -h`
- Error rates: Check application logs

### Weekly Checks

- Backup verification: Test restore process
- Security updates: `sudo apt upgrade`
- Performance metrics: Review monitoring dashboard

### Monthly Tasks

- Database optimization
- Log rotation configuration
- Update inventory of running versions
- Security patch assessment

---

*See TROUBLESHOOTING.md if deployment fails*
