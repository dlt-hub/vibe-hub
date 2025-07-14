In this guide, we'll set up a complete AccuLynx data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def acculynx_webhooks_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.acculynx.com/api/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "subscriptions",
            "topics",
            "notifications"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='acculynx_webhooks_pipeline',
        destination='duckdb',
        dataset_name='acculynx_webhooks_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(acculynx_webhooks_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from acculynx_webhooks’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Webhooks Management: Manage webhook subscriptions and notifications.
- Leads Management: Handle the creation of leads.

You can combine these endpoints to build pipelines that extract structured content from AccuLynx workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@acculynx_webhooks-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with AccuLynx support.
    ```shell
    dlt init dlthub:acculynx_webhooks duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@acculynx_webhooks-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for AccuLynx API, as specified in @acculynx_webhooks-docs.yaml 
    Start with endpoints "subscriptions" and "topics" and skip incremental loading for now. 
    Place the code in acculynx_webhooks_pipeline.py and name the pipeline acculynx_webhooks_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python acculynx_webhooks_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is required for all API requests using a Bearer token. The token should be placed in the Authorization header.
    
    To get appropriate API keys, please visit the original source at https://www.acculynx.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python acculynx_webhooks_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline acculynx_webhooks load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset acculynx_webhooks_data
    The duckdb destination used duckdb:/acculynx_webhooks.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline acculynx_webhooks_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("acculynx_webhooks_pipeline").dataset()
   # get "subscriptions" table as Pandas frame
   data."subscriptions".df().head()
    ```

## Running into errors?

The API requires an active AccuLynx account with administrative privileges and a specific API key per integration. The AccuLynx API enforces rate limits both on a per-IP and per-key basis, which should be carefully managed to avoid service disruption. Webhooks should be designed with security and performance in mind, including handling potential duplicate events and adhering to the 10-second timeout rule.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)