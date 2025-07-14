In this guide, we'll set up a complete Snapchat Marketing data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def snapchat_marketing_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://marketingapi.snapchat.com/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "adaccounts",
            "ads",
            "campaigns"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='snapchat_marketing_pipeline',
        destination='duckdb',
        dataset_name='snapchat_marketing_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(snapchat_marketing_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from snapchat_marketing’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Ad Management: Manage ads, campaigns, and related entities.
- Statistics: Retrieve performance data for ads, ad accounts, and campaigns in various time granularities.

You can combine these endpoints to build pipelines that extract structured content from Snapchat Marketing workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@snapchat_marketing-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Snapchat Marketing support.
    ```shell
    dlt init dlthub:snapchat_marketing duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Snapchat Marketing API, as specified in @snapchat_marketing-docs.yaml 
    Start with endpoints "adaccounts" and "ads" and skip incremental loading for now. 
    Place the code in snapchat_marketing_pipeline.py and name the pipeline snapchat_marketing_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python snapchat_marketing_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses OAuth2 with a refresh token flow. Authentication requires client ID, client secret, and refresh token. The token is placed in the header under 'Authorization'.
    
    To get appropriate API keys, please visit the original source at https://www.snapchat.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python snapchat_marketing_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline snapchat_marketing load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset snapchat_marketing_data
    The duckdb destination used duckdb:/snapchat_marketing.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline snapchat_marketing_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("snapchat_marketing_pipeline").dataset()
   # get adaccounts table as Pandas frame
   data.adaccounts.df().head()
    ```

## Running into errors?

Throttle API calls to avoid hitting request limits. Be aware of potential large data volumes when syncing data with hourly granularity. Ensure correct OAuth scopes are set as token expiry can lead to 401 Unauthorized errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)