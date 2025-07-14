In this guide, we'll set up a complete Taboola Backstage data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def taboola_backstage_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://backstage.taboola.com/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "account",
            "campaigns",
            "campaign_items"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='taboola_backstage_pipeline',
        destination='duckdb',
        dataset_name='taboola_backstage_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(taboola_backstage_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from taboola_backstage’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Accounts: Retrieve detailed account information.
- Campaigns: Access and manage advertising campaigns.
- Campaign Items: Manage individual items within a campaign.
- Audience Rules: Define and manage audience targeting rules.
- Conversion Rules: Set up and manage conversion tracking rules.
- Motion Ads: Handle motion ad specific configurations.
- Audiences: Manage data regarding audiences for targeted advertising.

You can combine these endpoints to build pipelines that extract structured content from Taboola Backstage workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@taboola_backstage-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Taboola Backstage support.
    ```shell
    dlt init dlthub:taboola_backstage duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Taboola Backstage API, as specified in @taboola_backstage-docs.yaml 
    Start with endpoints "account" and "campaigns" and skip incremental loading for now. 
    Place the code in taboola_backstage_pipeline.py and name the pipeline taboola_backstage_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python taboola_backstage_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via OAuth2 with refresh tokens. You need to provide client_id, client_secret, and account_id in the header for authentication.
    
    To get appropriate API keys, please visit the original source at https://www.taboola.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python taboola_backstage_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline taboola_backstage load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset taboola_backstage_data
    The duckdb destination used duckdb:/taboola_backstage.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline taboola_backstage_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("taboola_backstage_pipeline").dataset()
   # get account table as Pandas frame
   data.account.df().head()
    ```

## Running into errors?

There is no support for pagination in responses, only full sync is available. There are specific error messages for common issues like bad requests, unauthorized access, and not found errors which suggest checking request parameters, verifying credentials, and ensuring correct endpoint usage respectively.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)