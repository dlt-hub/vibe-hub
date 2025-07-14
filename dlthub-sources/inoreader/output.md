In this guide, we'll set up a complete Inoreader data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def inoreader_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://www.inoreader.com/reader/api/0/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "preference_stream_list",
            "rename_tag",
            "disable_tag"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='inoreader_pipeline',
        destination='duckdb',
        dataset_name='inoreader_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(inoreader_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from inoreader’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- User Management: Manage user information and authentication.
- Subscription Management: Handle feed subscriptions and their organization.
- Tag and Folder Management: Manage tags and folders for user-specific categorization.
- Content Retrieval: Fetch content based on stream or subscription details.
- Preferences and Configuration: Set user-specific preferences and configurations.

You can combine these endpoints to build pipelines that extract structured content from Inoreader workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@inoreader-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Inoreader support.
    ```shell
    dlt init dlthub:inoreader duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@inoreader-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Inoreader API, as specified in @inoreader-docs.yaml 
    Start with endpoints "preference_stream_list" and "rename_tag" and skip incremental loading for now. 
    Place the code in inoreader_pipeline.py and name the pipeline inoreader_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python inoreader_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses OAuth2 with authorization code flow for authentication. An access token is required for making API requests, which is obtained by using the client ID and client secret.
    
    To get appropriate API keys, please visit the original source at https://www.inoreader.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python inoreader_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline inoreader load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset inoreader_data
    The duckdb destination used duckdb:/inoreader.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline inoreader_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("inoreader_pipeline").dataset()
   # get "preference_stream_list" table as Pandas frame
   data."preference_stream_list".df().head()
    ```

## Running into errors?

API usage requires an Inoreader Pro plan for all non-public application use cases. There are default rate limits that are enforced daily, and limits can be increased for developing publicly available apps or via a Custom Inoreader plan only. The API does not collect sensitive data or transfer personal data outside the EEA.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)