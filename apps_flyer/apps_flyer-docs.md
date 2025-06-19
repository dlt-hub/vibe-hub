# How to load AppsFlyer data in Python using dlt

**Build a AppsFlyer-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def apps_flyer_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://dev.appsflyer.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/hc/docs/app-clip-sdk-integration",
            "/hc/docs/dl_android_unified_deep_linking",
            "/websdk.appsflyer.com"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='apps_flyer_pipeline',
        destination='duckdb',
        dataset_name='apps_flyer_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(apps_flyer_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from apps_flyer’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- App Integration: Documentation and guides for integrating AppsFlyer with applications.
- Deep Link Data: Endpoints for handling and retrieving data related to deep linking on Android and iOS platforms.
- Banner Management: Management of smart banners including showing, hiding, and updating banner parameters.
- Audience and Event Management: APIs to handle account connections, event tracking, and audience management.

You can combine these endpoints to build pipelines that extract structured content from AppsFlyer workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with AppsFlyer support.
    ```shell
    dlt init dlthub:apps_flyer duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for AppsFlyer API as specified in @apps_flyer-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in apps_flyer_pipeline.py and name the pipeline apps_flyer_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python apps_flyer_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@apps_flyer-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication uses OAuth2 with bearer tokens, requiring specific handling for token refresh and secure storage of tokens.
    
    To get appropriate API keys, please visit the original source at https://dev.app.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python apps_flyer_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline apps_flyer load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset apps_flyer_data
    The duckdb destination used duckdb:/apps_flyer.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline apps_flyer_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("apps_flyer_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Key caveats include handling the rate limits and daily quotas, daily data refresh rates, and specific requirements for deep linking parameters. Also, some APIs may not be available for agencies and partners, and certain configurations must be initially done in the UI.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)