In this guide, we'll set up a complete PaySimple data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def paysimple_payments_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.paysimple.com/v4/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "order",
            "card_sale",
            "recurring_payment"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='paysimple_payments_pipeline',
        destination='duckdb',
        dataset_name='paysimple_payments_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(paysimple_payments_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from paysimple_payments’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Transactions: Manage and process various types of transactions, including sales and recurring payments.
- Customer Management: Handle customer-related operations such as creating and managing customer accounts.
- Payment Methods: Manage payment methods, including credit card accounts and banking information.
- Custom Fields: Create, update, and manage custom fields for additional data collection.

You can combine these endpoints to build pipelines that extract structured content from PaySimple workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@paysimple_payments-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with PaySimple support.
    ```shell
    dlt init dlthub:paysimple_payments duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@paysimple_payments-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for PaySimple API, as specified in @paysimple_payments-docs.yaml 
    Start with endpoints "order" and "card_sale" and skip incremental loading for now. 
    Place the code in paysimple_payments_pipeline.py and name the pipeline paysimple_payments_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python paysimple_payments_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    PaySimple uses OAuth2 for authentication. The authorization is passed in the header using a bearer token. API keys and merchant IDs are required for different operations within the API.
    
    To get appropriate API keys, please visit the original source at https://www.paysimple.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python paysimple_payments_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline paysimple_payments load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset paysimple_payments_data
    The duckdb destination used duckdb:/paysimple_payments.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline paysimple_payments_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("paysimple_payments_pipeline").dataset()
   # get "order" table as Pandas frame
   data."order".df().head()
    ```

## Running into errors?

Ensure proper handling of payment data to maintain PCI compliance. Use PaySimple's checkout forms when possible to reduce PCI burden. Be aware of the limitations and requirements for device support and transaction processing, especially concerning Stripe integration and device compatibility. Handle webhook events carefully, as they may be delivered more than once.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)