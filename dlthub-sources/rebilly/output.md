In this guide, we'll set up a complete Rebilly data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def rebilly_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.rebilly.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "customers",
            "products",
            "orders"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='rebilly_pipeline',
        destination='duckdb',
        dataset_name='rebilly_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(rebilly_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from rebilly’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Customers: Manage customer profiles and details. 
- Products: Handle product catalog and details. 
- Orders: Process and manage orders, including POST methods for creation. 
- Payment Methods: Manage different forms of payment methods. 
- Payment Gateways: Handle various payment gateway configurations and transactions.

You can combine these endpoints to build pipelines that extract structured content from Rebilly workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@rebilly-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Rebilly support.
    ```shell
    dlt init dlthub:rebilly duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@rebilly-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Rebilly API, as specified in @rebilly-docs.yaml 
    Start with endpoints "customers" and "products" and skip incremental loading for now. 
    Place the code in rebilly_pipeline.py and name the pipeline rebilly_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python rebilly_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Rebilly uses API keys for authentication. It supports a refresh token flow where tokens can be refreshed using a client ID, client secret, and a refresh token.
    
    To get appropriate API keys, please visit the original source at https://www.rebilly.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python rebilly_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline rebilly load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset rebilly_data
    The duckdb destination used duckdb:/rebilly.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline rebilly_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("rebilly_pipeline").dataset()
   # get "customers" table as Pandas frame
   data."customers".df().head()
    ```

## Running into errors?

Rebilly implements strict rate limits, particularly in its sandbox environment where non-GET requests are limited to 3000 per 10 minutes. It is crucial to handle these limits in your application to avoid service disruption. Rebilly is a level 1 PCI-DSS and SOC 2 compliant service, which means it adheres to high security standards. However, data is not shared between live and sandbox environments, which requires separate management for each. Also, certain features like DCC come at an additional cost and require pre-authorization before use.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)