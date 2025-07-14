In this guide, we'll set up a complete RepairShopr data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def repair_shop_resources_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-subdomain.repairshopr.com/",
            "auth": {
                "type": "apikey",
                "token": access_token,
            },
        },
        "resources": [
            "appointment_types",
            "appointments",
            "customer_assets"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='repair_shop_resources_pipeline',
        destination='duckdb',
        dataset_name='repair_shop_resources_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(repair_shop_resources_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from repair_shop_resources’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Appointment Management: Allows the fetching and management of appointments and appointment types.
- Asset Management: Manages customer assets, providing data access and tracking.
- Contact Management: Facilitates the retrieval and management of contact information.
- Contract Management: Enables the management of contracts.
- Customer Management: Used to manage customer information and interactions.
- Financial Documents: Includes endpoints for managing estimates, invoices, and payments.
- Inventory Management: Concerns the management of items, products, and related details.
- Sales Lead Management: Manages sales leads data.
- Support Ticket Management: Handles customer support tickets.

You can combine these endpoints to build pipelines that extract structured content from RepairShopr workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@repair_shop_resources-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with RepairShopr support.
    ```shell
    dlt init dlthub:repair_shop_resources duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for RepairShopr API, as specified in @repair_shop_resources-docs.yaml 
    Start with endpoints "appointment_types" and "appointments" and skip incremental loading for now. 
    Place the code in repair_shop_resources_pipeline.py and name the pipeline repair_shop_resources_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python repair_shop_resources_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    This source uses an API key for authentication. The key should be included in the headers of all requests to authenticate access.
    
    To get appropriate API keys, please visit the original source at https://www.repairshopr.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python repair_shop_resources_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline repair_shop_resources load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset repair_shop_resources_data
    The duckdb destination used duckdb:/repair_shop_resources.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline repair_shop_resources_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("repair_shop_resources_pipeline").dataset()
   # get appointment_types table as Pandas frame
   data.appointment_types.df().head()
    ```

## Running into errors?

RepairShopr supports full synchronization but does not support incremental sync for any streams, which may affect data freshness and sync efficiency. Common errors include 401 Unauthorized, which suggests issues with the API key or subdomain, and 404 Not Found, indicating potential errors in endpoint paths or parameters.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)