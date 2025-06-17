# How to load Ticketmaster data in Python using dlt

**Build a Ticketmaster-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def ticketmaster_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://app.ticketmaster.com/discovery-feed/v2",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/events.json",
            "/events",
            "/EVENTS_RAW-AE-2a74db40-1abd-490f-ad08-308535515663-2024-10-31_192119.json.gz"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='ticketmaster_pipeline',
        destination='duckdb',
        dataset_name='ticketmaster_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(ticketmaster_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from ticketmaster’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Events Feed: Access event data per country or for all countries. - Download Event Feed File: Download specific event feed files in compressed format. - Discovery: Access detailed information about events, attractions, and venues. - Support: Contact support via a dedicated POST endpoint.

You can combine these endpoints to build pipelines that extract structured content from Ticketmaster workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Ticketmaster support.
    ```shell
    dlt init dlthub:ticketmaster duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Ticketmaster API as specified in @ticketmaster-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in ticketmaster_pipeline.py and name the pipeline ticketmaster_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python ticketmaster_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@ticketmaster-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    OAuth2 is used for authentication. The API key must be included in query parameters for each request.
    
    To get appropriate API keys, please visit the original source at https://developer.ticketmaster.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python ticketmaster_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline ticketmaster load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset ticketmaster_data
    The duckdb destination used duckdb:/ticketmaster.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline ticketmaster_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("ticketmaster_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

API key is essential for all requests. The API supports JSON and CSV formats, but XML is deprecated. Public APIs are limited to 2 requests per second and 5000 requests per day. Discovery Feed data is refreshed hourly and has no call limits. API terms can be terminated suddenly, and Ticketmaster can modify or discontinue any API aspect without notice. Errors must be managed by adhering to the API's usage and request structure requirements.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)