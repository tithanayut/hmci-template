# Homemade CI

This is how you can set up a deploy preview on a pull request to a remote host using GitHub Actions, Docker, and Traefik.

## How it works

## You will need

- A remote host with Linux installed (Ubuntu recommended)
- A GitHub repository

## Here are the instructions

First set up the server,

1. Install Docker on the remote host. It is easy to use the convenience script available [here](https://docs.docker.com/engine/install/linux-postinstall/).
2. Create a new user for GitHub Actions runner and assign the user to the docker group according to [Post-installation steps for Linux](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script).
3. Generate and assign new key pair for that user. GitHub Actions runner should be able to log in via SSH using the private key.
4. Clone this repo and transfer the `server` folder to the remote host.
5. Find and replace `example.com` and ` admin@example.com` with appropriate values.
6. Edit `traefik.toml` ACME DNS-01 challenge configuration to match the provider you are using according to the [documentation](https://doc.traefik.io/traefik/https/acme/). This step is required for obtaining Let's Encrypt wildcard TLS/SSL certificate. You may adjust the configuration to use TLS or HTTP challenge instead, but remember that this will make Traefik request a new certificate for every deploy preview since a new subdomain is created. There are [rate limits] for doing this (https://letsencrypt.org/docs/rate-limits/), so you better use the wildcard certificate.
7. Start docker-compose.yaml

Next, set up a DNS record to point \*.example.com to the remote host. Your DNS server/provider needs to support wildcard records.

Lastly, the Git-side.

1. Copy and commit `deploy-preview.yaml` and `dispose-preview.yaml` to the `.github/workflows/` directory on the main branch of your repository.
2. Config the following secrets in the GitHub repository setting.

| Name        | Value                                                                                                                                                                                |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| BASE_DOMAIN | Base domain of the deploy preview. For example, setting this as `example.com` will let the template create a new deploy preview at `preview-pr1.example.com` where 1 is a PR number. |
| VM_HOST     | Hostname or IP of the remote host.                                                                                                                                                   |
| VM_USERNAME | Username you created in step 2 of the server setup.                                                                                                                                  |
| VM_KEY      | Private key of the user above.                                                                                                                                                       |
| CR_USERNAME | Username to authenticate with ghcr.io (typically your GitHub username)                                                                                                               |
| CR_PAT      | GitHub Personal Access Token with at least read:packages, write:packages, and delete:packages scope. to                                                                              |

You can now check if it is working or not by opening a PR that branches from the main branch. After a short while, you should see the comment from the GitHub Actions bot for the deploy preview URL. You can always adjust the configuration to your preference (like using another container registry)!.
