# How to load Younium data in Python using dlt

**Build a Younium-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def younium_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.younium.com/api-s/api-2.0",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/api/measurements",
            "/Orders/{id}",
            "/Orders/Charges/{id}"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='younium_pipeline',
        destination='duckdb',
        dataset_name='younium_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(younium_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from younium’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Financial Management: Manage and view financial accounts, invoices, and payments.
- Sales and Orders: Handle sales orders, subscriptions, and charges.
- Product Management: Manage and retrieve information about products and tax templates.
- Reporting: Access detailed reports on various aspects of the system.
- Data Management: Manage data related to bookings, journals, and measurements.

You can combine these endpoints to build pipelines that extract structured content from Younium workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Younium support.
    ```shell
    dlt init dlthub:younium duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Younium API as specified in @younium-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in younium_pipeline.py and name the pipeline younium_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python younium_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@younium-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is performed using OAuth2 with JWT tokens. The access token must be included in the header of each API request.
    
    To get appropriate API keys, please visit the original source at https://developer.younium.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python younium_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline younium load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset younium_data
    The duckdb destination used duckdb:/younium.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline younium_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("younium_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Authentication errors such as 401 Unauthorized or 403 Forbidden indicate issues with token expiry or permissions. Ensure correct legal entity IDs and permissions. Also, idempotent requests have a default storage duration of 24 hours with a maximum of 168 hours. Webhooks are retried every 3 hours up to 10 times before requiring manual intervention.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)