# How to load SafetyCulture data in Python using dlt

**Build a SafetyCulture-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def safety_culture_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.safetyculture.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/accounts/scim/v2",
            "/integrations/v1/apps",
            "/integrations/v1/apps/{app_id}/installations"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='safety_culture_pipeline',
        destination='duckdb',
        dataset_name='safety_culture_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(safety_culture_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from safety_culture’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Platform management: Manage organizations, sites, credentials, and users.
- SCIM User Provisioning: Manage user provisioning via SCIM protocol.
- Asset Management: Handle operations related to assets.
- Inspections: Manage and retrieve inspections and their related items.
- Issues Management: Operations related to issue tracking and management.
- Single Sign-On: Manage authentication through single sign-on capabilities.

You can combine these endpoints to build pipelines that extract structured content from SafetyCulture workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with SafetyCulture support.
    ```shell
    dlt init dlthub:safety_culture duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for SafetyCulture API as specified in @safety_culture-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in safety_culture_pipeline.py and name the pipeline safety_culture_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python safety_culture_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@safety_culture-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is managed through OAuth2 with bearer token authentication. The API token must be passed in the Authorization header for each request.
    
    To get appropriate API keys, please visit the original source at https://developer.safetyculture.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python safety_culture_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline safety_culture load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset safety_culture_data
    The duckdb destination used duckdb:/safety_culture.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline safety_culture_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("safety_culture_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

API rate limits are enforced, and certain endpoints may require specific permissions or plan levels. Ensure that inspections with media are fully synced before export to avoid corrupt files. The API uses OAuth 2.0 with Resource Owner Password Credentials Grant, and API tokens expire after 30 days of inactivity.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)