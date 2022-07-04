# Homemade CI

This is how you can setup deploy preview on pull request to remote host using GitHub Actions, Docker, and Traefik.

You will need:

- A remote host with Linux installed (Ubuntu recommended)
- A GitHub repository

Here are the instructions:

First setup the server,

1. Install Docker on the remote host. It is easy to use the convenience script available [here](https://docs.docker.com/engine/install/linux-postinstall/).
2. Create new user for GitHub Actions runner and assign the said user to docker group according to [Post-installation steps for Linux](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script).
3. Generate and assign new key pair for that user. GitHub Actions runner should be able to login via SSH using private key.
4. Clone this repo and transfer `server` folder to the remote host.
5. Find and replace `example.com` and ` admin@example.com` with appropriate values.
6. Edit `traefik.toml` ACME DNS-01 challenge configuration to match the provider you are using according to the [documentation](https://doc.traefik.io/traefik/https/acme/). This step is required for obtaining Let's Encrypt wildcard TLS/SSL certificate. You may adjust the configuration to use TLS or HTTP challenge, but remember that there are [rate limits](https://letsencrypt.org/docs/rate-limits/). The script was designed to request new certificate for a sub-domain every time there is a new PR created, so you better use the wildcard certificate.
7. Start docker-compose.yaml
