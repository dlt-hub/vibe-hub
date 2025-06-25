In this guide, we'll set up a complete Marketo data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def marketo_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.mktorest.com/rest/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "activities_send_email",
            "activity_types",
            "campaigns"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='marketo_pipeline',
        destination='duckdb',
        dataset_name='marketo_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(marketo_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from marketo’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Activities: Manage and retrieve information on various marketing activities.
- Campaigns: Operations related to marketing campaigns including retrieval and management.
- Leads: Functions to handle leads including creation, update, and retrieval.
- Lists: Operations to work with lists of leads or other entities.
- Programs: Retrieve and manage marketing programs.
- Segmentations: Handle segmentation logic and retrieve segmentations.

You can combine these endpoints to build pipelines that extract structured content from Marketo workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@marketo-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Marketo support.
    ```shell
    dlt init dlthub:marketo duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Marketo API, as specified in @marketo-docs.yaml 
    Start with endpoints "activities_send_email" and "activity_types" and skip incremental loading for now. 
    Place the code in marketo_pipeline.py and name the pipeline marketo_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python marketo_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The Marketo API uses OAuth2 authentication. A refresh token, client ID, and client secret are used to obtain an access token from a token URL. The access token is then used for subsequent requests, passed in the Authorization header.
    
    To get appropriate API keys, please visit the original source at https://www.marketo.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python marketo_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline marketo load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset marketo_data
    The duckdb destination used duckdb:/marketo.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline marketo_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("marketo_pipeline").dataset()
   # get activities_send_email table as Pandas frame
   data.activities_send_email.df().head()
    ```

## Running into errors?

The Marketo API has a default call limit of 50,000 calls per day, but the connector is capped at 40,000 API calls per day. It's important to handle these limits by throttling API calls or reducing the frequency to avoid hitting these limits. Additionally, handling errors like '401 Unauthorized' requires rechecking OAuth scopes or token expiration.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)