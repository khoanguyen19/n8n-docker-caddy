# n8n Docker Deployment with Caddy

This repository contains Docker configuration for deploying n8n with Caddy as a reverse proxy and PostgreSQL as the database.

## Prerequisites

- A server running Linux (Ubuntu/Debian recommended)
- Docker and Docker Compose installed
- Domain name pointed to your server (for SSL)
- Basic understanding of Docker and server administration

## Installation Steps

1. **Clone the Repository**
   ```bash
   git clone https://github.com/yourusername/n8n-docker-caddy.git
   cd n8n-docker-caddy
   ```

2. **Open required ports**
   ```bash
   sudo ufw allow 80  # Allow HTTP traffic
   sudo ufw allow 443 # Allow HTTPS traffic
   sudo ufw allow {CUSTOM_PORT} # Allow custom port traffic
   ```

3. **Configure Environment Variables**
   ```bash
   cp .env.example .env
   ```
   Edit the `.env` file and update:
   - `DOMAIN_NAME`: Your domain name
   - `SUBDOMAIN`: Your preferred subdomain
   - `POSTGRES_USER`: Database username
   - `POSTGRES_PASSWORD`: Strong database password
   - `N8N_ENCRYPTION_KEY`: Random string for encryption
   - `N8N_USER_MANAGEMENT_JWT_SECRET`: Random string for JWT

4. **Create External Docker Volume**
   ```bash
   docker volume create caddy_data
   ```

5. **Configure Caddy**
   Create `caddy_config/Caddyfile` with your domain configuration:
   ```
   {$SUBDOMAIN}.{$DOMAIN_NAME} {
       reverse_proxy n8n:5678
   }
   ```

6. **Start the Stack**
   ```bash
   docker-compose up -d
   ```

## Backup and Restore

### Creating Backups
- Workflows and credentials are automatically backed up in `./n8n/backup/`
- Database backups should be handled separately using PostgreSQL tools

### Restoring Data
- Place workflow files in `./n8n/backup/workflows/`
- Place credential files in `./n8n/backup/credentials/`
- Restart the stack: `docker-compose restart`

## Security Considerations

1. Always change default passwords in `.env`
2. Keep your `N8N_ENCRYPTION_KEY` secure - losing it means losing access to credentials
3. Regular backups are recommended
4. Keep Docker and n8n updated

## Maintenance

### Updating n8n
```bash
docker-compose pull
docker-compose up -d
```

### Viewing Logs
```bash
docker-compose logs -f n8n
```

## Troubleshooting

1. **Cannot access n8n**
   - Check if containers are running: `docker-compose ps`
   - Verify Caddy logs: `docker-compose logs caddy`
   - Ensure firewall allows ports 80 and 443

2. **Database Connection Issues**
   - Check PostgreSQL logs: `docker-compose logs postgres`
   - Verify database credentials in `.env`

## Support

For more information and support:
- [n8n Documentation](https://docs.n8n.io/)
- [n8n Community Forums](https://community.n8n.io/)
- [Caddy Documentation](https://caddyserver.com/docs/)

## License

This project is licensed under the MIT License - see the LICENSE file for details.
