In this guide, we'll set up a complete 2Smart Cloud data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def smart_cloud_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.api-name.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "devices",
            "users",
            "products"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='smart_cloud_pipeline',
        destination='duckdb',
        dataset_name='smart_cloud_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(smart_cloud_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from smart_cloud’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Device Management: Manage IoT devices connected to the platform.
- User Management: Handle user accounts and their permissions.
- Product Management: Manage product records and details.
- Sensor Readings: Retrieve data from various sensors like temperature, humidity, etc.
- Firmware Management: Handle firmware updates and installations for devices.

You can combine these endpoints to build pipelines that extract structured content from 2Smart Cloud workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@smart_cloud-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with 2Smart Cloud support.
    ```shell
    dlt init dlthub:smart_cloud duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@smart_cloud-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for 2Smart Cloud API, as specified in @smart_cloud-docs.yaml 
    Start with endpoints "devices" and "users" and skip incremental loading for now. 
    Place the code in smart_cloud_pipeline.py and name the pipeline smart_cloud_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python smart_cloud_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The API uses OAuth2 with a refresh token flow. Authentication requires setting up a connected app on 2Smart Cloud, using client credentials and a refresh token to obtain an access token.
    
    To get appropriate API keys, please visit the original source at https://www.2smart.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python smart_cloud_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline smart_cloud load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset smart_cloud_data
    The duckdb destination used duckdb:/smart_cloud.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline smart_cloud_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("smart_cloud_pipeline").dataset()
   # get "devices" table as Pandas frame
   data."devices".df().head()
    ```

## Running into errors?

The API uses self-signed certificates which may require additional configuration in client applications to trust these certificates. It operates under a strict request limit, so throttling API calls is necessary to avoid exceeding these limits. It's important to handle 401 Unauthorized errors by rechecking OAuth scopes or token expiration.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)