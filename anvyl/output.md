In this guide, we'll set up a complete Anvyl data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def anvyl_operations_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.api-name.com/api/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "workflow_milestones",
            "shipment",
            "suppliers"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='anvyl_operations_pipeline',
        destination='duckdb',
        dataset_name='anvyl_operations_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(anvyl_operations_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from anvyl_operations’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Workflow Management: Manage workflows and milestones for purchase orders. 
- Shipment Management: Handle shipment creation, updates, and tracking. 
- Custom Fields Management: Create, update, and manage custom field values for various data points. 
- Supplier Management: Manage supplier details, including bulk operations and specific supplier identifiers. 
- Order Management: Handle creation, cancellation, and updates of purchase and assembly orders. 
- Payment Terms: Manage and update payment terms associated with teams and orders.

You can combine these endpoints to build pipelines that extract structured content from Anvyl workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@anvyl_operations-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Anvyl support.
    ```shell
    dlt init dlthub:anvyl_operations duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@anvyl_operations-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Anvyl API, as specified in @anvyl_operations-docs.yaml 
    Start with endpoints "workflow_milestones" and "shipment" and skip incremental loading for now. 
    Place the code in anvyl_operations_pipeline.py and name the pipeline anvyl_operations_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python anvyl_operations_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses OAuth2 with refresh token flow for authentication. Requires client ID, client secret, and refresh token. Tokens and other sensitive credentials should be stored securely.
    
    To get appropriate API keys, please visit the original source at https://www.anvyl.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python anvyl_operations_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline anvyl_operations load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset anvyl_operations_data
    The duckdb destination used duckdb:/anvyl_operations.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline anvyl_operations_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("anvyl_operations_pipeline").dataset()
   # get "workflow_milestones" table as Pandas frame
   data."workflow_milestones".df().head()
    ```

## Running into errors?

Be aware of the API rate limits, which are 20,000 calls per hour for single calls and 500 calls per minute for bulk operations. Additionally, some endpoints are deprecated and should be replaced with newer endpoints as suggested in the documentation. Proper error handling for common HTTP status codes like 401, 403, and 422 is crucial.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)