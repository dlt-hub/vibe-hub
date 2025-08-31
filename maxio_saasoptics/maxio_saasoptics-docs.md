In this guide, we'll set up a complete Maxio data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def maxio_financial_services_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "http://www.maxio.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "subscriptions",
            "offers",
            "invoices"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='maxio_financial_services_pipeline',
        destination='duckdb',
        dataset_name='maxio_financial_services_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(maxio_financial_services_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from maxio_financial_services’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Subscriptions: Allows the creation and management of customer subscriptions.
- Offers: Manages promotional or pricing offers.
- Usage: Tracks and posts usage data for billing components linked to subscriptions.
- Invoices: Retrieves invoice data for specific customer transactions.
- Customer Portal: Enables or disables access for customers to the self-service portal.

You can combine these endpoints to build pipelines that extract structured content from Maxio workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@maxio_financial_services-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Maxio support.
    ```shell
    dlt init dlthub:maxio_financial_services duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@maxio_financial_services-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Maxio API, as specified in @maxio_financial_services-docs.yaml 
    Start with endpoints "subscriptions" and "offers" and skip incremental loading for now. 
    Place the code in maxio_financial_services_pipeline.py and name the pipeline maxio_financial_services_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python maxio_financial_services_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication for Maxio uses OAuth 2.0 with a refresh token flow. The token URL is 'https://login.maxio.com/oauth2/token'. Credentials such as the client ID, client secret, and refresh token are stored securely and are required to obtain an access token.
    
    To get appropriate API keys, please visit the original source at https://www.maxio.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python maxio_financial_services_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline maxio_financial_services load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset maxio_financial_services_data
    The duckdb destination used duckdb:/maxio_financial_services.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline maxio_financial_services_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("maxio_financial_services_pipeline").dataset()
   # get "subscriptions" table as Pandas frame
   data."subscriptions".df().head()
    ```

## Running into errors?

Maxio processes personal data which requires strict adherence to privacy policies and compliance with data protection regulations. Users must ensure secure handling of authentication tokens and sensitive data.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)