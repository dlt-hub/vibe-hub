# How to load Adjust data in Python using dlt

**Build a Adjust-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def adjust_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://dev.adjust.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
,
        },
        "resources": [
            "/app",
            "/cost",
            "/reports"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='adjust_pipeline',
        destination='duckdb',
        dataset_name='adjust_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(adjust_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from adjust’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- App Management: Endpoints to create, view, update, and manage app-related information.
- Link Management: Endpoints to generate, update, and manage deep links and blocklist/unblocklist links.
- Reporting: Endpoints to retrieve different types of reports such as standard, CSV, and pivot reports.
- Device Management: Endpoints to inspect or forget devices associated with specific app tokens and advertising IDs.

You can combine these endpoints to build pipelines that extract structured content from Adjust workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Adjust support.
    ```shell
    dlt init dlthub:adjust duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Adjust API as specified in @adjust-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in adjust_pipeline.py and name the pipeline adjust_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python adjust_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@adjust-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is performed using a Bearer token which should be included in the Authorization header of each API request. API tokens are managed in the user's profile section under Account settings.
    
    To get appropriate API keys, please visit the original source at https://dev.adjust.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python adjust_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline adjust load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset adjust_data
    The duckdb destination used duckdb:/adjust.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline adjust_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("adjust_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Important considerations include ensuring that JavaScript is enabled in the WebView, managing SDK integration with proper framework additions based on iOS version and targeted features, and handling different permissions and configurations for iOS and Android. Be aware of the TTL for short links being 120 days and ensure proper error handling as suggested by various 4xx and 5xx error responses.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)