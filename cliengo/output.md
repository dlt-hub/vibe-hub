In this guide, we'll set up a complete Cliengo data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def cliengo_migrations_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.cliengo.com/1.0/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "conversations",
            "users",
            "contacts"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='cliengo_migrations_pipeline',
        destination='duckdb',
        dataset_name='cliengo_migrations_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(cliengo_migrations_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from cliengo_migrations’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Conversations: Manage chat conversations, including creating, updating, and fetching specific conversation details.
- Users: Retrieve information about users registered on the platform.
- Messages: Manage messages within conversations, including listing, adding, and updating messages.
- Account: Access and modify account details.
- Sites: Retrieve information about sites associated with the account, including details about chatbots.

You can combine these endpoints to build pipelines that extract structured content from Cliengo workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@cliengo_migrations-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Cliengo support.
    ```shell
    dlt init dlthub:cliengo_migrations duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@cliengo_migrations-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Cliengo API, as specified in @cliengo_migrations-docs.yaml 
    Start with endpoints "conversations" and "users" and skip incremental loading for now. 
    Place the code in cliengo_migrations_pipeline.py and name the pipeline cliengo_migrations_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python cliengo_migrations_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via JWT (JSON Web Tokens) with a flow of refreshing tokens. The token should be included in the query parameters for authentication.
    
    To get appropriate API keys, please visit the original source at https://www.cliengo.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python cliengo_migrations_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline cliengo_migrations load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset cliengo_migrations_data
    The duckdb destination used duckdb:/cliengo_migrations.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline cliengo_migrations_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("cliengo_migrations_pipeline").dataset()
   # get "conversations" table as Pandas frame
   data."conversations".df().head()
    ```

## Running into errors?

Endpoints that handle array data support pagination using limit and offset parameters to help manage data loads and avoid duplicate records. However, the API may include underdeveloped or restricted endpoints, which require whitelisting or have specific usage limits. Frequent calls to certain methods might be restricted, and a timeout of 5 seconds is recommended to begin with.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)