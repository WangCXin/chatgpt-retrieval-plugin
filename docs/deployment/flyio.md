# Deploying to Fly.io

## Removing Unused Dependencies

Before deploying your app, you might want to remove unused dependencies from your [pyproject.toml](/pyproject.toml) file to reduce the size of your app and improve its performance. Depending on the vector database provider you choose, you can remove the packages that are not needed for your specific provider.

Find the packages you can remove for each vector database provider [here](removing-unused-dependencies.md).

After removing the unnecessary packages from the `pyproject.toml` file, you don't need to run `poetry lock` and `poetry install` manually. The provided Dockerfile takes care of installing the required dependencies using the `requirements.txt` file generated by the `poetry export` command.

## Deployment

To deploy the Docker container from this repository to Fly.io, follow
these steps:

[Install Docker](https://docs.docker.com/engine/install/) on your local machine if it is not already installed.

Install the [Fly.io CLI](https://fly.io/docs/getting-started/installing-flyctl/) on your local machine.

Clone the repository from GitHub:

```
git clone https://github.com/openai/chatgpt-retrieval-plugin.git
```

Navigate to the cloned repository directory:

```
cd path/to/chatgpt-retrieval-plugin
```

Log in to the Fly.io CLI:

```
flyctl auth login
```

Create and launch your Fly.io app:

```
flyctl launch
```

Follow the instructions in your terminal:

- Choose your app name
- Choose your app region
- Don't add any databases
- Don't deploy yet (if you do, the first deploy might fail as the environment variables are not yet set)

Set the required environment variables:

```
flyctl secrets set DATASTORE=your_datastore \
OPENAI_API_KEY=your_openai_api_key \
BEARER_TOKEN=your_bearer_token \
<Add the environment variables for your chosen vector DB here>
```

Alternatively, you could set environment variables in the [Fly.io Console](https://fly.io/dashboard).

At this point, you can change the plugin url in your plugin manifest file [here](/.well-known/ai-plugin.json), and in your OpenAPI schema [here](/.well-known/openapi.yaml) to the url for your Fly.io app, which will be `https://your-app-name.fly.dev`.

Deploy your app with:

```
flyctl deploy
```

After completing these steps, your Docker container should be deployed to Fly.io and running with the necessary environment variables set. You can view your app by running:

```
flyctl open
```

which will open your app url. You should be able to find the OpenAPI schema at `<your_app_url>/.well-known/openapi.yaml` and the manifest at `<your_app_url>/.well-known/ai-plugin.json`.

To view your app logs:

```
flyctl logs
```

Now, make sure you have changed the plugin url in your plugin manifest file [here](/.well-known/ai-plugin.json), and in your OpenAPI schema [here](/.well-known/openapi.yaml), and redeploy with `flyctl deploy`. This url will be `https://<your-app-name>.fly.dev`.

**Debugging tips:**
Fly.io uses port 8080 by default.

If your app fails to deploy, check if the environment variables are set correctly, and then check if your port is configured correctly. You could also try using the [`-e` flag](https://fly.io/docs/flyctl/launch/) with the `flyctl launch` command to set the environment variables at launch.