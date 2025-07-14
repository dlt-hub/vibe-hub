In this guide, we'll set up a complete Zoho Invoice data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def zoho_invoice_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://www.zoho.com/invoice/api/v3/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "items",
            "users",
            "contacts"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='zoho_invoice_pipeline',
        destination='duckdb',
        dataset_name='zoho_invoice_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(zoho_invoice_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from zoho_invoice’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Items: Manage and retrieve items data.
- Users: Handle user information and permissions.
- Contacts: Access contact details of businesses and individuals.
- Invoices: Generate, update, and manage invoices.
- Recurring Invoices: Manage and automate recurring invoices.
- Customer Payments: Track and process customer payments.
- Credit Notes: Manage and issue credit notes to customers.
- Expenses: Record and manage business expenses.
- Taxes: Handle various tax rates and their applications.

You can combine these endpoints to build pipelines that extract structured content from Zoho Invoice workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@zoho_invoice-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Zoho Invoice support.
    ```shell
    dlt init dlthub:zoho_invoice duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Zoho Invoice API, as specified in @zoho_invoice-docs.yaml 
    Start with endpoints "items" and "users" and skip incremental loading for now. 
    Place the code in zoho_invoice_pipeline.py and name the pipeline zoho_invoice_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python zoho_invoice_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Zoho Invoice uses OAuth2 for authentication. It requires a client_id, client_secret, and refresh_token to obtain an access token. The tokens should be passed in the Authorization header as a Bearer token.
    
    To get appropriate API keys, please visit the original source at https://www.zoho.com/invoice/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python zoho_invoice_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline zoho_invoice load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset zoho_invoice_data
    The duckdb destination used duckdb:/zoho_invoice.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline zoho_invoice_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("zoho_invoice_pipeline").dataset()
   # get items table as Pandas frame
   data.items.df().head()
    ```

## Running into errors?

Requires organization_id if the user belongs to multiple organizations. Ensure correct client credentials and organization_id to avoid 401 Unauthorized errors. Also, verify request parameters and data formats to prevent 400 Bad Request errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)