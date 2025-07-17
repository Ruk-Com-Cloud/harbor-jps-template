# Harbor Registry with HTTPS - Jelastic JPS

A comprehensive Jelastic JPS (Jelastic Packaging Standard) template for deploying Harbor registry with automatic HTTPS certificates, load balancing, and high availability.

## 🚀 One-Click Deploy

Deploy Harbor registry to your Jelastic environment with a single click:

[![Deploy to Jelastic](https://github.com/Ruk-Com-Cloud/simple-jps/blob/main/deploy-to-ruk-com.png?raw=true)](https://app.manage.ruk-com.cloud/?manifest=https://raw.githubusercontent.com/Ruk-Com-Cloud/harbor-jps-template/refs/heads/main/manifest.jps)

## Features

- **🔒 Automatic HTTPS**: SSL certificates with auto-renewal
- **⚖️ Load Balancing**: Nginx load balancer for high availability
- **🐳 Harbor Registry**: Full-featured Docker registry with web UI, vulnerability scanning, and Helm charts
- **🗄️ PostgreSQL Backend**: Reliable PostgreSQL 17 database for Harbor metadata
- **💾 Redis Cache**: Redis 7 for caching and job queues
- **📊 Health Monitoring**: Built-in health check endpoints
- **🔧 Version Selection**: Choose from multiple Harbor versions (v2.9.0 to v2.13.1)

## Architecture

```text
Internet → Nginx Load Balancer (SSL) → Harbor Instances
                                             ↓
                                 PostgreSQL ← → Redis
```

### Components

| Component | Node Type | Count | Resources | Purpose |
|-----------|-----------|-------|-----------|---------|
| Nginx LB | nginx | 1 | 8 cloudlets | Load balancing and SSL termination |
| Harbor Core | dockerengine | 1 | 32 cloudlets | Container registry instance |
| PostgreSQL | postgres17 | 1 | 16 cloudlets | Database backend |
| Redis | redis7 | 1 | 8 cloudlets | Cache and job queues |

**Total Resources**: 64 cloudlets

## Quick Start

### 1. Deploy via One-Click Button

Click the "Deploy to Jelastic" button above and follow the installation wizard.

### 2. Deploy via Jelastic Marketplace

1. Log into your Jelastic control panel
2. Go to **Marketplace** → **Import**
3. Upload the `harbor-registry.jps` file
4. Select Harbor version from dropdown
5. Click **Install**

### 3. Deploy via Jelastic CLI

```bash
jelastic environment.control.createenvironmentfromfile \
  --file harbor-registry.jps \
  --envName harbor-registry \
  --settings '{"harborVersion":"v2.13.1"}'
```

## Configuration

### Supported Harbor Versions

- v2.13.1 (Latest - Default)
- v2.12.0
- v2.11.1
- v2.10.3
- v2.9.0

### SSL Configuration

The installation automatically:

- Configures Nginx with SSL termination
- Applies security headers (HSTS, XSS protection, etc.)
- Enables HTTP to HTTPS redirect
- Provides production-ready SSL configuration

### Load Balancer Configuration

Nginx load balancer features:

- **Health Checks**: Automatic failover support
- **Large Uploads**: Supports up to 1GB file uploads
- **Streaming**: Optimized for Docker image layers
- **Health Endpoint**: `/health` for monitoring

## Usage

### Access Harbor

After deployment, access Harbor at:

- **URL**: `https://your-domain.com`
- **Username**: `admin`
- **Password**: Generated during installation (shown in success message)

### Docker Client Configuration

```bash
# Login to Harbor
docker login your-domain.com

# Tag and push image
docker tag myimage:latest your-domain.com/myproject/myimage:latest
docker push your-domain.com/myproject/myimage:latest

# Pull image
docker pull your-domain.com/myproject/myimage:latest
```

### Health Monitoring

Check system health:

```bash
# Load balancer health
curl https://your-domain.com/health

# Harbor instance health
curl http://harbor-instance-ip:80/api/v2.0/health
```

## Maintenance

### Scaling Harbor Instances

Harbor can be scaled horizontally by adding more instances to the `cp` node group in the Jelastic dashboard.

### Database Maintenance

PostgreSQL maintenance tasks:

```bash
# Connect to database
psql -h postgres-ip -U harbor -d harbor

# Check database size
SELECT pg_size_pretty(pg_database_size('harbor'));

# Vacuum and analyze
VACUUM ANALYZE;
```

### Redis Maintenance

Redis maintenance:

```bash
# Connect to Redis
redis-cli -h redis-ip -a redis-password

# Check memory usage
INFO memory

# Clear cache if needed
FLUSHDB
```

## Troubleshooting

### Common Issues

1. **Database Connection Error**
   - Check PostgreSQL service status
   - Verify database credentials in Harbor config
   - Ensure network connectivity between nodes

2. **SSL Certificate Issues**
   - Verify domain DNS points to load balancer
   - Check Nginx logs: `tail -f /var/log/nginx/error.log`
   - Ensure port 80/443 are accessible

3. **Harbor Startup Issues**
   - Check Docker Compose status: `docker-compose ps`
   - View Harbor logs: `docker-compose logs -f`
   - Verify database connectivity

### Log Locations

- **Nginx**: `/var/log/nginx/access.log`, `/var/log/nginx/error.log`
- **Harbor**: `docker-compose logs -f` (in `/opt/harbor/`)
- **PostgreSQL**: `/var/log/postgresql/`
- **Redis**: `/var/log/redis/`

## Security

### Best Practices

1. **Change Default Passwords**: Update Harbor admin password after installation
2. **Enable RBAC**: Configure role-based access control for projects
3. **Image Scanning**: Enable Trivy vulnerability scanning
4. **Network Security**: Use Jelastic private network for inter-node communication
5. **Regular Updates**: Keep Harbor and dependencies updated

### Security Features

- HTTPS with SSL certificates
- Security headers (HSTS, XSS protection, CSRF protection)
- Private network communication between nodes
- Database credential encryption
- Redis authentication

## Requirements

- Jelastic PaaS account
- Minimum 64 cloudlets available
- Domain name pointing to your Jelastic environment
- Support for Docker containers

## Support

For issues and questions:

1. Check the [troubleshooting section](#troubleshooting)
2. Review Harbor documentation: <https://goharbor.io/docs/>
3. Check Jelastic documentation for node management
4. Create an issue in this repository

## Contributing

1. Fork the repository
2. Make your changes
3. Test the JPS deployment
4. Submit a pull request

## License

This project is licensed under the MIT License.

## Changelog

### v1.0.0

- Initial release with Harbor registry
- Nginx load balancer with SSL termination
- PostgreSQL and Redis backend services
- Support for multiple Harbor versions
- Comprehensive health monitoring
- One-click deployment support
