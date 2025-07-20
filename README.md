# Host Langflow

This repo contains a Dockerfile to help you host Langflow with any hosting provider that supports Docker.

## Usage

The directory structure here helps you deploy to specific targets. Follow this guidance:

- The `bm` directory is for bare metal deployments where you have root access to an actual machine.
- The root is a typical Dockerfile for any standard hosting provider like [Flightcontrol](https://flightcontrol.dev), [Fly.io](https://fly.io), or [Render](https://render.com).

### Hosting on Azure

If you'd like to host Langflow on Azure, follow these 5 steps:

1. Make sure you have the Azure CLI:

   `brew install azure-cli`

2. Login to Azure:

   `az login`

3. Create a resource group:

   `az group create --name langflowRG --location eastus`

4. Deploy Langflow:

   ```bash
   # Use the same resource group you created in step 3
   az container create --resource-group langflowRG \
                       --name langflow-server \
                       --image langflowai/langflow:latest \
                       --dns-name-label mylangflowapp-$(openssl rand -hex 4) \
                       --ports 7860 \
                       --os-type Linux
   ```

> :bulb: The `$(openssl rand -hex 4)` or a similar mechanism helps create a unique DNS name label.

5. Azure will output the Fully Qualified Domain Name (FQDN) once deployed. You can then access your Langflow at `http://<your-dns-name-label>.<region>.azurecontainer.io:7860`.




### Hosting on hetzner

1. git clone {this repo}
2. cd host-langflow
3. cd bm
4. snap install docker
5. docker-compose up 






