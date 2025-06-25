In this guide, we'll set up a complete Microsoft Graph SharePoint Sites data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def sharepoint_sites_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://graph.microsoft.com/v1.0/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "lists",
            "listcontenttypes",
            "listitems"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='sharepoint_sites_pipeline',
        destination='duckdb',
        dataset_name='sharepoint_sites_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(sharepoint_sites_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from sharepoint_sites’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Lists: Manage and retrieve lists in a SharePoint site.
- List Content Types: Access content types within a specific list.
- List Items: Operations on items within a list.

You can combine these endpoints to build pipelines that extract structured content from Microsoft Graph SharePoint Sites workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@sharepoint_sites-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Microsoft Graph SharePoint Sites support.
    ```shell
    dlt init dlthub:sharepoint_sites duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Microsoft Graph SharePoint Sites API, as specified in @sharepoint_sites-docs.yaml 
    Start with endpoints "lists" and "listcontenttypes" and skip incremental loading for now. 
    Place the code in sharepoint_sites_pipeline.py and name the pipeline sharepoint_sites_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python sharepoint_sites_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is handled via OAuth2 with a refresh token flow. The authorization requires specific Microsoft Graph API permissions such as Sites.Read.All and Sites.ReadWrite.All.
    
    To get appropriate API keys, please visit the original source at https://www.microsoft.com/en-us/microsoft-365/sharepoint/collaboration.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python sharepoint_sites_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline sharepoint_sites load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset sharepoint_sites_data
    The duckdb destination used duckdb:/sharepoint_sites.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline sharepoint_sites_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("sharepoint_sites_pipeline").dataset()
   # get lists table as Pandas frame
   data.lists.df().head()
    ```

## Running into errors?

Authentication requires careful management of client IDs, secrets, and tokens, including handling token expiration. Permissions must be correctly set up and granted in the Microsoft Azure portal. API responses and accessibility might vary based on the user's SharePoint site configurations and access rights.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)