In this guide, we'll set up a complete Azure Blob Storage data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def azure_blob_storage_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://<your_storage_account>.blob.core.windows.net/",
            "auth": {"type": "bearer", "token": access_token,}
,
        },
        "resources": [
            "blob_container", "list", "create"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='azure_blob_storage_pipeline',
        destination='duckdb',
        dataset_name='azure_blob_storage_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(azure_blob_storage_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from azure_blob_storage’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Blob Service: Handles operations on blob containers and blobs, including listing, creating, and deleting containers and blobs. - Authentication: Provides details on how to authenticate using Azure AD for accessing Blob service securely. - Error Handling: Describes different HTTP status codes and their meanings to troubleshoot access issues.

You can combine these endpoints to build pipelines that extract structured content from Azure Blob Storage workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@azure_blob_storage-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Azure Blob Storage support.
    ```shell
    dlt init dlthub:azure_blob_storage duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Azure Blob Storage API, as specified in @azure_blob_storage-docs.yaml 
    Start with endpoints "blob_container" and "list" and skip incremental loading for now. 
    Place the code in azure_blob_storage_pipeline.py and name the pipeline azure_blob_storage_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python azure_blob_storage_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication uses Azure Active Directory with a client credentials flow. A tenant ID, client ID, and client secret are required to obtain an access token.
    
    To get appropriate API keys, please visit the original source at https://azure.microsoft.com/en-us/services/storage/blobs/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python azure_blob_storage_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline azure_blob_storage load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset azure_blob_storage_data
    The duckdb destination used duckdb:/azure_blob_storage.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline azure_blob_storage_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("azure_blob_storage_pipeline").dataset()
   # get blob_container table as Pandas frame
   data.blob_container.df().head()
    ```

## Running into errors?

Requires the role of Storage Blob Data Reader for accessing blob data. Egress costs may apply when transferring data out of Azure Blob Storage. Proper permissions are essential to avoid 403 Forbidden errors. Ensure the correct tenant ID, client ID, and client secret are used to avoid 401 Unauthorized errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)