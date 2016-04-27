Pgpool2 Dockerfile
==================

This project can be used to deploy pgpool2 inside a Docker container for transparent failover between two postgresql hosts without requiring a floating IP address.

### Running the Container

```sudo docker run --name pgpool2 -e PGPOOL_BACKENDS=1:127.0.0.1:5432,2:127.0.0.1:5433 -p 5432:5432/tcp bettervoice/pgpool2-container:3.3.4```

### Configuration Environment Variables

**PCP_PORT** - The port used to listen for PCP commands. (default: 9898)

**PCP_USER** - The user allowed to execute PCP commands. (default: postgres)

**PCP_USER_PASSWORD** - The pcp user password. (default: bettervoice)

**PGPOOL_PORT** - The port used by pgpool2 to listen for client connections. (default: 5432)

**PGPOOL_BACKENDS** - A comma separated list of PostgeSQL server backends. The format for each backend is as follows: INDEX:HOST:PORT (default: 1:localhost:5432)
