In this guide, we'll set up a complete Fastly data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def fastly_service_management_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.fastly.com/",
            "auth": {
                "type": "apikey",
                "token": access_token,
                "header_name": "Fastly-Key"
            },
        },
        "resources": [
            "current_user",
            "service",
            "service_version"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='fastly_service_management_pipeline',
        destination='duckdb',
        dataset_name='fastly_service_management_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(fastly_service_management_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from fastly_service_management’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- User Management: Manage user account settings.
- Service Configuration: Retrieve and manage configurations of services including versions, dictionaries, backends, domains, and access control lists.

You can combine these endpoints to build pipelines that extract structured content from Fastly workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@fastly_service_management-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Fastly support.
    ```shell
    dlt init dlthub:fastly_service_management duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Fastly API, as specified in @fastly_service_management-docs.yaml 
    Start with endpoints "current_user" and "service" and skip incremental loading for now. 
    Place the code in fastly_service_management_pipeline.py and name the pipeline fastly_service_management_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python fastly_service_management_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via an API key passed in the header of each request. The key should be stored securely and passed as 'Fastly-Key' in the header.
    
    To get appropriate API keys, please visit the original source at https://www.fastly.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python fastly_service_management_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline fastly_service_management load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset fastly_service_management_data
    The duckdb destination used duckdb:/fastly_service_management.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline fastly_service_management_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("fastly_service_management_pipeline").dataset()
   # get current_user table as Pandas frame
   data.current_user.df().head()
    ```

## Running into errors?

Ensure the Fastly API token has the appropriate scope for your use case. Be aware of rate limiting and implement exponential backoff as necessary. Check token validity and permissions regularly to avoid unauthorized errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)