# Troubleshooting Guide

## Quick Diagnosis

**Something isn't working?** Answer these 3 questions:

1. **When did it break?** Immediately after deploy? Recently? Always?
2. **What's the error message?** Search the table below by error code
3. **What were you doing?** Which feature/endpoint were you using?

---

## Error Code Reference

### Error: E001 - Connection Refused

**Symptoms:**
- "Cannot connect to localhost:3000"
- "ECONNREFUSED"

**Why it happens:**
- Service not running
- Wrong port configured
- Firewall blocking

**Solution:**

```bash
# Check if service is running
sudo systemctl status [project-name]

# If stopped, start it
sudo systemctl start [project-name]

# Check logs for errors
journalctl -u [project-name] -n 50

# Verify port is listening
sudo netstat -tlnp | grep 3000
```

**Prevention:**
- Use systemd to auto-restart on failure
- Set up monitoring alerts

---

### Error: E002 - Database Connection Failed

**Symptoms:**
- "Cannot connect to database"
- "ENOTFOUND localhost"
- "Authentication failed"

**Why it happens:**
- Database not running
- Wrong credentials in .env
- Database doesn't exist
- Network connectivity issue

**Solution:**

```bash
# Check database is running
sudo systemctl status postgresql

# Verify connectivity
psql -U appuser -d appdb -h localhost -c "SELECT 1;"

# Check credentials in .env
cat .env | grep DATABASE_URL

# If credentials wrong, update .env and restart
sudo systemctl restart [project-name]

# Check application logs
journalctl -u [project-name] -n 100 | grep -i database
```

**Prevention:**
- Test database connectivity before deploying
- Rotate credentials regularly
- Monitor database uptime

---

### Error: E003 - SSL Certificate Error

**Symptoms:**
- "Self signed certificate"
- "Certificate expired"
- "Unable to verify"

**Why it happens:**
- Certificate not found
- Certificate expired
- Wrong path configured

**Solution:**

```bash
# Check certificate exists
ls -la /etc/letsencrypt/live/yourdomain.com/

# Check expiry
openssl x509 -enddate -noout -in /path/to/cert.pem

# Renew if expired
sudo certbot renew

# Verify paths in .env match actual locations
```

**Prevention:**
- Set up auto-renewal
- Monitor certificate expiry
- Alert 30 days before expiry

---

### Error: E004 - Out of Memory

**Symptoms:**
- "JavaScript heap out of memory"
- Process killed by OOM killer
- Slow response times

**Why it happens:**
- Memory leak
- Too many concurrent connections
- Insufficient server RAM

**Solution:**

```bash
# Check memory usage
free -h
ps aux --sort=-%mem | head

# Restart service to free memory
sudo systemctl restart [project-name]

# Increase Node.js memory limit
NODE_OPTIONS="--max-old-space-size=4096" npm start
```

**Prevention:**
- Monitor memory usage
- Set up alerts at 80% usage
- Scale vertically or horizontally

---

## Common Issues Matrix

| Issue | Error Message | Cause | Solution |
|-------|---------------|-------|----------|
| App won't start | "Port 3000 already in use" | Another process | `sudo lsof -i :3000` then kill |
| Slow response | Response time > 5s | High load / N+1 queries | See performance section |
| Memory leak | Memory grows constantly | Unclosed connections | Restart, check code |
| SSL errors | "self signed certificate" | Cert misconfigured | Verify cert path |
| CORS errors | "No Access-Control-Allow-Origin" | Config issue | Check CORS settings |
| 502 Bad Gateway | Nginx error | App not responding | Check app logs |

---

## Performance Troubleshooting

### Issue: High CPU Usage

```bash
# Identify hot processes
top -p $(pgrep node)

# Check for long-running operations
journalctl -u [project-name] -n 200 | grep SLOW

# Profile application
curl http://localhost:3000/admin/profile
```

**Common causes:**
- N+1 database queries - Implement query batching
- Inefficient algorithms - Profile and optimize
- Runaway loops - Check recent code changes

---

### Issue: High Memory Usage

```bash
# Check memory breakdown
ps aux | grep node

# If growing constantly = memory leak
# Restart immediately
sudo systemctl restart [project-name]

# Investigate
journalctl -u [project-name] --since "1 hour ago" | grep -i memory
```

---

### Issue: Slow Database Queries

```bash
# Enable slow query logging
# In postgresql.conf:
log_min_duration_statement = 1000

# Find slow queries
grep duration /var/log/postgresql/postgresql-*.log
```

---

## Debugging Techniques

### Technique 1: Enable Verbose Logging

```bash
# Set debug mode
export DEBUG=*
export LOG_LEVEL=debug

# Restart service
sudo systemctl restart [project-name]

# Watch logs
journalctl -u [project-name] -f
```

### Technique 2: Check Application Logs

```bash
# Last 100 lines
journalctl -u [project-name] -n 100

# Errors only
journalctl -u [project-name] -p err

# Since specific time
journalctl -u [project-name] --since "2 hours ago"

# Follow real-time
journalctl -u [project-name] -f
```

### Technique 3: Health Check

```bash
# Basic health
curl http://localhost:3000/health

# Detailed diagnostics
curl http://localhost:3000/diagnostics | jq .

# Database test
curl http://localhost:3000/admin/db-test

# External connectivity
curl http://localhost:3000/admin/connectivity-check
```

### Technique 4: Network Diagnostics

```bash
# Check listening ports
sudo netstat -tlnp

# Test connectivity
curl -v http://localhost:3000

# Check DNS
nslookup yourdomain.com

# Check firewall
sudo ufw status
```

---

## Known Limitations

⚠️ **Scale Limit:** Handles up to 10,000 concurrent connections. Beyond that requires clustering.

⚠️ **Storage Limit:** Database performance degrades with > 1GB data. Implement archiving.

⚠️ **Feature X:** Not supported on Windows (requires Linux system calls)

⚠️ **File Upload:** Maximum 100MB per file

---

## Frequently Asked Questions

### Q: How do I reset the database?
```bash
# WARNING: This deletes all data
npm run db:reset
```

### Q: How do I update to a new version?
```bash
git pull
npm ci --production
npm run build
sudo systemctl restart [project-name]
```

### Q: Where are the logs?
- Application: `journalctl -u [project-name]`
- Nginx: `/var/log/nginx/`
- PostgreSQL: `/var/log/postgresql/`

---

## Support

- Check this guide first
- Found a bug? [Report it](https://github.com/.../issues)
- Questions? [Discussions](https://github.com/.../discussions)
- Commercial support: support@company.com

---

*Last updated: [DATE]*
*Version: v[X.Y.Z]*
