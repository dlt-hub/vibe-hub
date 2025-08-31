In this guide, we'll set up a complete WorkWave Route Manager data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def workwave_route_manager_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.workwave.com/api/v1/",
            "auth": {
                "type": "apikey",
                "header_name": "X-WorkWave-Key",
                "key": access_token
            },
        },
        "resources": [
            "territories",
            "drivers",
            "regions"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='workwave_route_manager_pipeline',
        destination='duckdb',
        dataset_name='workwave_route_manager_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(workwave_route_manager_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from workwave_route_manager’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Territories Management: Provides endpoints for managing territories, including simulations, regions, drivers, and vehicles.
- Scheduling: Includes endpoints for unassigning orders and managing scheduling within a simulation context.
- Order Management: Handles the addition, replacement, and updating of orders within simulations.
- Notifications: Deals with webhook notifications and event-based updates regarding plan changes.

You can combine these endpoints to build pipelines that extract structured content from WorkWave Route Manager workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@workwave_route_manager-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with WorkWave Route Manager support.
    ```shell
    dlt init dlthub:workwave_route_manager duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@workwave_route_manager-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for WorkWave Route Manager API, as specified in @workwave_route_manager-docs.yaml 
    Start with endpoints "territories" and "drivers" and skip incremental loading for now. 
    Place the code in workwave_route_manager_pipeline.py and name the pipeline workwave_route_manager_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python workwave_route_manager_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is achieved using an API key. The key should be included in the request header under the name 'X-WorkWave-Key'.
    
    To get appropriate API keys, please visit the original source at https://www.workwave.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python workwave_route_manager_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline workwave_route_manager load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset workwave_route_manager_data
    The duckdb destination used duckdb:/workwave_route_manager.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline workwave_route_manager_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("workwave_route_manager_pipeline").dataset()
   # get "territories" table as Pandas frame
   data."territories".df().head()
    ```

## Running into errors?

The API employs a complex set of rate limiting and throttling mechanisms, including a leaky bucket algorithm. Different endpoints have different rate limits, and these limits are also affected by the type of request (e.g., GET, POST, DELETE). It's essential to handle these limits appropriately to avoid request failures. Additionally, geocoding may not always be accurate, especially for new or incorrectly formatted addresses.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)