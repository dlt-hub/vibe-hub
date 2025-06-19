# How to load App Store Connect data in Python using dlt

**Build a App Store Connect-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def app_store_connect_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.apple.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/accessories/design/guidelines",
            "/homekit/specification",
            "/mfi/details"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='app_store_connect_pipeline',
        destination='duckdb',
        dataset_name='app_store_connect_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(app_store_connect_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from app_store_connect’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Accessory and HomeKit Specifications: Provides guidelines and specifications for designing accessories and integrating with HomeKit.
- App Management and Analytics: Offers endpoints for uploading builds, managing webhooks, and accessing analytics for apps.
- TestFlight and Feedback: Endpoints for managing TestFlight testers and collecting their feedback.
- App Store Features: Includes endpoints for managing game center features, app tags, and age ratings.

You can combine these endpoints to build pipelines that extract structured content from App Store Connect workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with App Store Connect support.
    ```shell
    dlt init dlthub:app_store_connect duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for App Store Connect API as specified in @app_store_connect-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in app_store_connect_pipeline.py and name the pipeline app_store_connect_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python app_store_connect_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@app_store_connect-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Uses OAuth2 authentication with a refresh token flow. Authentication requires a client ID, client secret, and refresh token to obtain an access token.
    
    To get appropriate API keys, please visit the original source at https://developer.app.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python app_store_connect_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline app_store_connect load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset app_store_connect_data
    The duckdb destination used duckdb:/app_store_connect.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline app_store_connect_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("app_store_connect_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Integration with certain Apple frameworks does not require API authentication. Pay attention to the authentication scopes and token expiration. Ensure compatibility with updated metrics and localization services to avoid inaccuracies.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)