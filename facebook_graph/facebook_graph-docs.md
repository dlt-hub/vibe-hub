# How to load Facebook Graph API data in Python using dlt

**Build a Facebook Graph API-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def facebook_graph_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://graph.facebook.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/ads_management",
            "/user_data",
            "/security"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='facebook_graph_pipeline',
        destination='duckdb',
        dataset_name='facebook_graph_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(facebook_graph_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from facebook_graph’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Ads Management: Manage ad campaigns, ad sets, and ads.
- User Data: Retrieve user profiles, interests, and interactions.
- Security: Log security events and manage account security settings.

You can combine these endpoints to build pipelines that extract structured content from Facebook Graph API workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Facebook Graph API support.
    ```shell
    dlt init dlthub:facebook_graph duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Facebook Graph API API as specified in @facebook_graph-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in facebook_graph_pipeline.py and name the pipeline facebook_graph_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python facebook_graph_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@facebook_graph-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is managed via OAuth 2.0 with bearer tokens. Tokens must be refreshed using the provided refresh token URL, and specific scopes are required for different endpoints.
    
    To get appropriate API keys, please visit the original source at https://developers.facebook.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python facebook_graph_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline facebook_graph load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset facebook_graph_data
    The duckdb destination used duckdb:/facebook_graph.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline facebook_graph_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("facebook_graph_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Ensure proper token management as tokens expire in 2 hours. Only one active token per integration is allowed, and generating a new token invalidates the previous one. Compliance with Meta Platform Terms and local legal restrictions is required.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)