In this guide, we'll set up a complete Column data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def column_financial_services_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.column.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "outgoing_transfer", "incoming_transfer", "return_request"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='column_financial_services_pipeline',
        destination='duckdb',
        dataset_name='column_financial_services_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(column_financial_services_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from column_financial_services’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Transfers: Handles both incoming and outgoing real-time financial transfers, including international wires and ACH transactions. - Returns and Cancellations: Manages return requests and cancellations for various types of transfers. - Account Management: Facilitates the creation and management of bank accounts and associated transactions. - Payment Services: Includes endpoints for real-time payments, book transfers, and card program management. - Reporting: Provides detailed reports on bank account transactions and summaries.

You can combine these endpoints to build pipelines that extract structured content from Column workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@column_financial_services-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Column support.
    ```shell
    dlt init dlthub:column_financial_services duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@column_financial_services-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Column API, as specified in @column_financial_services-docs.yaml 
    Start with endpoints "outgoing_transfer" and "incoming_transfer" and skip incremental loading for now. 
    Place the code in column_financial_services_pipeline.py and name the pipeline column_financial_services_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python column_financial_services_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via OAuth 2.0 with the need for a refresh token to maintain session validity. The API key is required for each request, passed through the header.
    
    To get appropriate API keys, please visit the original source at https://www.column.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python column_financial_services_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline column_financial_services load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset column_financial_services_data
    The duckdb destination used duckdb:/column_financial_services.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline column_financial_services_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("column_financial_services_pipeline").dataset()
   # get "outgoing_transfer" table as Pandas frame
   data."outgoing_transfer".df().head()
    ```

## Running into errors?

Users must be aware of the compliance requirements such as the Bank Secrecy Act and Anti-Money Laundering regulations. All financial transactions like ACH and wire transfers must adhere to specific timelines and rules. Real-time transfer statuses are subject to change, and webhook configurations are necessary for receiving updates. Idempotency keys are used for safe retry mechanisms but expire after 30 days.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)