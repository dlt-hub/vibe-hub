# How to load Deputy data in Python using dlt

**Build a Deputy-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def deputy_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://{install}.{geo}.deputy.com/api/v1",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/api/v1/resource/Leave",
            "/api/v1/supervise/company",
            "/api/v1/supervise/department"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='deputy_pipeline',
        destination='duckdb',
        dataset_name='deputy_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(deputy_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from deputy’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Employee Management: Manage employee details, agreements, and payroll settings.
- Roster Management: Handle shift scheduling, publishing, and updates.
- Metrics and Reporting: Configure and retrieve various metrics related to sales, staff requirements, and budgets.

You can combine these endpoints to build pipelines that extract structured content from Deputy workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Deputy support.
    ```shell
    dlt init dlthub:deputy duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Deputy API as specified in @deputy-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in deputy_pipeline.py and name the pipeline deputy_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python deputy_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@deputy-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is primarily via OAuth 2.0, requiring a bearer token included in the Authorization header. Refresh tokens are used to maintain session continuity.
    
    To get appropriate API keys, please visit the original source at https://developer.deputy.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python deputy_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline deputy load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset deputy_data
    The duckdb destination used duckdb:/deputy.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline deputy_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("deputy_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Ensure synchronization between Deputy and partner sessions to avoid login issues. Be aware of the 24-hour expiry of access tokens and manage refresh tokens appropriately. The API's behavior might vary slightly between the Premium and Enterprise versions, and additional security setups could be needed for certain endpoints.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)