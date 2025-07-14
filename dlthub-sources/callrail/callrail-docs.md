# How to load CallRail data in Python using dlt

**Build a CallRail-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def callrail_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.callrail.com/v3",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/v3/a/{account_id}/calls/{call_id}.json",
            "/v3/a/{account_id}/users/{user_id}.json",
            "/v3/a/{account_id}/forms/summary.json"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='callrail_pipeline',
        destination='duckdb',
        dataset_name='callrail_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(callrail_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from callrail’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Call Management: Manage and retrieve details about calls, including creating, updating, and deleting call logs.
- User Management: Handle user information including creating, updating, and deleting users.
- Form and Call Summaries: Retrieve summarized data about forms and calls, supporting various filters and groupings.
- Tag Management: Manage tags for categorization and segmentation of calls and forms.

You can combine these endpoints to build pipelines that extract structured content from CallRail workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with CallRail support.
    ```shell
    dlt init dlthub:callrail duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for CallRail API as specified in @callrail-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in callrail_pipeline.py and name the pipeline callrail_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python callrail_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@callrail-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is done via API Key. The API Key must be included in the request headers under the name 'Authorization' with the prefix 'Token'.
    
    To get appropriate API keys, please visit the original source at https://apidocs.callrail.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python callrail_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline callrail load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset callrail_data
    The duckdb destination used duckdb:/callrail.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline callrail_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("callrail_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Only users associated with the API key can manage their own names and emails. Passwords cannot be updated via the API. Users must have roles of manager, reporting, or notification to manage company access. API Key is user-specific; attempts to manage data for other users will result in an error. No JSON response for DELETE methods; HTTP 204 indicates success.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)