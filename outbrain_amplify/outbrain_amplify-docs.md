In this guide, we'll set up a complete Outbrain Amplify data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def outbrain_amplify_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://amplifyv01.api.outbrain.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "marketers",
            "campaigns/by_marketers",
            "campaigns/geo_location"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='outbrain_amplify_pipeline',
        destination='duckdb',
        dataset_name='outbrain_amplify_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(outbrain_amplify_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from outbrain_amplify’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Marketers: Access information about marketers.
- Campaigns by Marketers: Retrieve campaigns associated with specific marketers, with an option for incremental updates based on the 'updated_at' parameter.
- Campaigns Geo Location: Fetch geographical locations associated with campaigns.
- Promoted Links for Campaigns: Obtain links being promoted in specific campaigns.
- Budgets for Marketers: View budget details for marketers.
- Performance Reports: Generate various performance reports for campaigns, marketers, and content across different dimensions such as platforms, publishers, geo performance, and interests.

You can combine these endpoints to build pipelines that extract structured content from Outbrain Amplify workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@outbrain_amplify-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Outbrain Amplify support.
    ```shell
    dlt init dlthub:outbrain_amplify duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Outbrain Amplify API, as specified in @outbrain_amplify-docs.yaml 
    Start with endpoints "marketers" and "campaigns/by_marketers" and skip incremental loading for now. 
    Place the code in outbrain_amplify_pipeline.py and name the pipeline outbrain_amplify_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python outbrain_amplify_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via OAuth 2.0 with refresh token flow. Credentials include client ID, client secret, and refresh token. The token is passed in the header as 'Authorization'.
    
    To get appropriate API keys, please visit the original source at https://www.outbrain.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python outbrain_amplify_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline outbrain_amplify load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset outbrain_amplify_data
    The duckdb destination used duckdb:/outbrain_amplify.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline outbrain_amplify_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("outbrain_amplify_pipeline").dataset()
   # get marketers table as Pandas frame
   data.marketers.df().head()
    ```

## Running into errors?

The API supports both full refresh and incremental syncs but requires proper setup including credentials and a start-date. It has specific request limits, so throttling API calls or reducing request frequency might be necessary to avoid exceeding these limits. Error handling for '401 Unauthorized' should verify OAuth scopes or token expiration.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)