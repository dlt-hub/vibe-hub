In this guide, we'll set up a complete Firehydrant data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def firehydrant_api_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://app.firehydrant.io/api/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "environments",
            "services",
            "functionalities"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='firehydrant_api_pipeline',
        destination='duckdb',
        dataset_name='firehydrant_api_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(firehydrant_api_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from firehydrant_api’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Environments: Manage and retrieve information about environments.
- Services: Operations related to services in the infrastructure.
- Functionalities: Handle functionalities within services.
- Teams: Manage team-related functionalities.
- Webhooks: Set up and manage webhooks.
- Signals on Call: Retrieve signals related to on-call duties.
- Changes and Events: Track and manage change events.
- Incidents: Operations related to incident management including roles, tags, and types.
- Integrations: Manage integrations with other services.
- Users: User management and retrieval.
- Reports: Generate and retrieve reports.
- Runbooks: Manage runbooks, their actions, executions, and audits.

You can combine these endpoints to build pipelines that extract structured content from Firehydrant workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@firehydrant_api-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Firehydrant support.
    ```shell
    dlt init dlthub:firehydrant_api duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Firehydrant API, as specified in @firehydrant_api-docs.yaml 
    Start with endpoints "environments" and "services" and skip incremental loading for now. 
    Place the code in firehydrant_api_pipeline.py and name the pipeline firehydrant_api_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python firehydrant_api_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication requires an API token provided in the header of requests.
    
    To get appropriate API keys, please visit the original source at https://www.firehydrant.io/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python firehydrant_api_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline firehydrant_api load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset firehydrant_api_data
    The duckdb destination used duckdb:/firehydrant_api.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline firehydrant_api_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("firehydrant_api_pipeline").dataset()
   # get environments table as Pandas frame
   data.environments.df().head()
    ```

## Running into errors?

Authentication is crucial and requires a valid API token. Rate limits apply to endpoints, necessitating a strategy for handling 429 status codes such as retry logic or exponential backoff.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)