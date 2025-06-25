In this guide, we'll set up a complete PayPal data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def paypal_transactions_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.paypal.com/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "transactions",
            "balances",
            "list_products"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='paypal_transactions_pipeline',
        destination='duckdb',
        dataset_name='paypal_transactions_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(paypal_transactions_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from paypal_transactions’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Reporting Services: Includes endpoints for transactions and balances. 
- Product Catalog: Manages product listings and details. 
- Dispute Resolution: Handles customer disputes. 
- Invoicing Services: Search and management of invoices. 
- Payment Services: Includes operations related to payments.

You can combine these endpoints to build pipelines that extract structured content from PayPal workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@paypal_transactions-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with PayPal support.
    ```shell
    dlt init dlthub:paypal_transactions duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for PayPal API, as specified in @paypal_transactions-docs.yaml 
    Start with endpoints "transactions" and "balances" and skip incremental loading for now. 
    Place the code in paypal_transactions_pipeline.py and name the pipeline paypal_transactions_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python paypal_transactions_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    PayPal uses OAuth 2.0 for authentication, requiring a client ID, client secret, and a refresh token to obtain an access token.
    
    To get appropriate API keys, please visit the original source at https://www.paypal.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python paypal_transactions_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline paypal_transactions load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset paypal_transactions_data
    The duckdb destination used duckdb:/paypal_transactions.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline paypal_transactions_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("paypal_transactions_pipeline").dataset()
   # get transactions table as Pandas frame
   data.transactions.df().head()
    ```

## Running into errors?

PayPal API has limitations such as a maximum of 50 requests per minute per IP and no more than 3 years of historical transaction data retrieval. Large result sets must be reduced, and OAuth scopes or token validity must be verified upon receiving a 401 Unauthorized error.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)