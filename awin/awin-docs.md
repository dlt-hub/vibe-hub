# How to load Awin data in Python using dlt

**Build a Awin-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def awin_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.awin.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/transactions",
            "/advertisers/{advertiserId}/transactions/jobs",
            "/s2s/advertiser/{advertiser_id}/orders"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='awin_pipeline',
        destination='duckdb',
        dataset_name='awin_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(awin_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from awin’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Transactions: Manage and query transaction details, including creating, amending, and querying transactions.
- Orders: Post details about orders directly through server-to-server communication.
- Webhooks: Set up listeners for various events like successful orders to trigger notifications or other actions.
- Reporting: Access performance data for creative assets, advertisers, and campaigns, allowing for detailed analysis and reporting.
- Authentication and Configuration: Endpoints for setting and retrieving configurations, such as commission groups and program details.

You can combine these endpoints to build pipelines that extract structured content from Awin workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Awin support.
    ```shell
    dlt init dlthub:awin duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Awin API as specified in @awin-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in awin_pipeline.py and name the pipeline awin_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python awin_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@awin-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is primarily handled using OAuth 2.0 with a bearer token. An API key is required for specific endpoints like 'Create Transactions'.
    
    To get appropriate API keys, please visit the original source at https://developer.awin.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python awin_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline awin load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset awin_data
    The duckdb destination used duckdb:/awin.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline awin_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("awin_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Be aware of the API call limits, which can throttle to 20 requests per minute. Also, ensure the correct handling of JSON and JSONL formats where specified, and manage authentication tokens securely. Some endpoints may require specific user permissions or plan subscriptions.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)