In this guide, we'll set up a complete API Name data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def api_source_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.api-name.com/v5/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "folders",
            "emails",
            "engagement_studio_programs"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='api_source_pipeline',
        destination='duckdb',
        dataset_name='api_source_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(api_source_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from api_source’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Folders: Retrieve folder structures and their metadata.
- Emails: Access and manage email records.
- Engagement Studio Programs: Interact with engagement programs.
- Forms: Manipulate form data and structure.
- Landing Pages: Manage landing page designs and content.
- Lifecycle Stages: Handle lifecycle stage data for CRM purposes.

You can combine these endpoints to build pipelines that extract structured content from API Name workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@api_source-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with API Name support.
    ```shell
    dlt init dlthub:api_source duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for API Name API, as specified in @api_source-docs.yaml 
    Start with endpoints "folders" and "emails" and skip incremental loading for now. 
    Place the code in api_source_pipeline.py and name the pipeline api_source_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python api_source_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The API uses OAuth2 authentication with a flow that involves refresh tokens. Tokens are acquired via a designated token URL using client credentials and refresh tokens. The 'Authorization' header is used for passing the access token.
    
    To get appropriate API keys, please visit the original source at https://api-docs.api-name.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python api_source_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline api_source load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset api_source_data
    The duckdb destination used duckdb:/api_source.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline api_source_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("api_source_pipeline").dataset()
   # get folders table as Pandas frame
   data.folders.df().head()
    ```

## Running into errors?

There are some potential issues such as encountering nulls in deeply nested fields, hitting the request limit which requires throttling API calls, handling query timeouts by optimizing filters, and managing OAuth token expiration and scope issues.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)