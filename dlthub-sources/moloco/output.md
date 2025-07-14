In this guide, we'll set up a complete Moloco Cloud data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def moloco_cloud_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.moloco.cloud/cm/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "logs",
            "customer-sets",
            "audience-targets"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='moloco_cloud_pipeline',
        destination='duckdb',
        dataset_name='moloco_cloud_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(moloco_cloud_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from moloco_cloud’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Log Management: Allows for the creation and status checking of logs.
- Customer Data Management: Handles operations on customer sets.
- Audience Targeting: Manages audience targets for campaigns.
- Ad Management: Includes endpoints for managing ad groups, ad accounts, and specific ads.
- Product Information: Manages product listings.
- Tracking: Handles creation and management of tracking links.
- Creative Content: Facilitates operations on creatives and creative groups.
- Campaign Management: Provides endpoints for creating and managing campaigns.
- Reporting: Enables creating reports and checking their status.

You can combine these endpoints to build pipelines that extract structured content from Moloco Cloud workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@moloco_cloud-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Moloco Cloud support.
    ```shell
    dlt init dlthub:moloco_cloud duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@moloco_cloud-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Moloco Cloud API, as specified in @moloco_cloud-docs.yaml 
    Start with endpoints "logs" and "customer-sets" and skip incremental loading for now. 
    Place the code in moloco_cloud_pipeline.py and name the pipeline moloco_cloud_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python moloco_cloud_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication requires OAuth2 with a refresh token flow. Tokens must be included in the header under 'Authorization'.
    
    To get appropriate API keys, please visit the original source at https://www.moloco.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python moloco_cloud_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline moloco_cloud load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset moloco_cloud_data
    The duckdb destination used duckdb:/moloco_cloud.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline moloco_cloud_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("moloco_cloud_pipeline").dataset()
   # get "logs" table as Pandas frame
   data."logs".df().head()
    ```

## Running into errors?

Log API is disabled by default and can be enabled upon request. There are strict API call limits that, when exceeded, can throttle requests. There are also entity creation quotas per workplace, and all creative assets must be uploaded externally before use. An API for direct creative uploads is planned. Campaigns default to a 'SUBMITTED' status and require time for ML model training. Tokens are valid for 16 hours, and secure connections (TLS v1.2 or later) are required.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)