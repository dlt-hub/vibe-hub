In this guide, we'll set up a complete Dwolla data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def dwolla_migrations_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.dwolla.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "funding-sources",
            "customers",
            "transfers"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='dwolla_migrations_pipeline',
        destination='duckdb',
        dataset_name='dwolla_migrations_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(dwolla_migrations_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from dwolla_migrations’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Funding Sources: Manage and retrieve information about funding sources such as bank accounts.
- Customers: Create and manage customer information and their verification status.
- Transfers: Handle money transfers between Dwolla accounts, check transfer status, and manage related fees.

You can combine these endpoints to build pipelines that extract structured content from Dwolla workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@dwolla_migrations-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Dwolla support.
    ```shell
    dlt init dlthub:dwolla_migrations duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Dwolla API, as specified in @dwolla_migrations-docs.yaml 
    Start with endpoints "funding-sources" and "customers" and skip incremental loading for now. 
    Place the code in dwolla_migrations_pipeline.py and name the pipeline dwolla_migrations_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python dwolla_migrations_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Dwolla uses OAuth2 for authentication. A client ID and client secret are used to obtain a bearer token via a client credentials flow from a token URL. This token is then used in the Authorization header for subsequent requests.
    
    To get appropriate API keys, please visit the original source at https://www.dwolla.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python dwolla_migrations_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline dwolla_migrations load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset dwolla_migrations_data
    The duckdb destination used duckdb:/dwolla_migrations.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline dwolla_migrations_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("dwolla_migrations_pipeline").dataset()
   # get funding-sources table as Pandas frame
   data.funding-sources.df().head()
    ```

## Running into errors?

Transfers have processing times and are subject to ACH rules, which may delay transactions. The API includes rate limits that must be respected to avoid 429 Too Many Requests errors. Webhook validation requires processing HMAC SHA-256 to ensure security.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)