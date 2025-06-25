In this guide, we'll set up a complete Copper data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def copper_crm_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.copper.com/developer_api/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "account",
            "users",
            "activities"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='copper_crm_pipeline',
        destination='duckdb',
        dataset_name='copper_crm_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(copper_crm_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from copper_crm’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- User Management: Includes endpoints for fetching and managing users, such as retrieving current API user or searching for users.
- Account Management: Provides access to account details.
- Activities Management: Handles creation, update, search, and deletion of activities, supporting bulk operations as well.
- Company Management: Focuses on bulk creating and updating companies, with specific constraints on request size and unique email domains.

You can combine these endpoints to build pipelines that extract structured content from Copper workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@copper_crm-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Copper support.
    ```shell
    dlt init dlthub:copper_crm duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Copper API, as specified in @copper_crm-docs.yaml 
    Start with endpoints "account" and "users" and skip incremental loading for now. 
    Place the code in copper_crm_pipeline.py and name the pipeline copper_crm_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python copper_crm_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication uses API keys requiring the header 'X-PW-AccessToken' for each request. OAuth 2.0 is supported for more secure interactions, utilizing tokens obtained through an authorization code grant flow.
    
    To get appropriate API keys, please visit the original source at https://www.copper.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python copper_crm_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline copper_crm load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset copper_crm_data
    The duckdb destination used duckdb:/copper_crm.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline copper_crm_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("copper_crm_pipeline").dataset()
   # get account table as Pandas frame
   data.account.df().head()
    ```

## Running into errors?

Copper's API is in beta, implying possible future changes and instability. Rate limits are strictly enforced, with a general limit of 3 requests per second and specific limits on bulk operations. Special attention must be paid to managing unique email domains in company data. Authentication requires careful management of headers, including user email and application-specific fields.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)