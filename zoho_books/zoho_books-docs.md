In this guide, we'll set up a complete Zoho Books data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def zoho_books_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://{region}.zoho.com/books/api/v3/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "users",
            "banking",
            "organizations"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='zoho_books_pipeline',
        destination='duckdb',
        dataset_name='zoho_books_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(zoho_books_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from zoho_books’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Users: Access user details.
- Banking: Manage banking transactions.
- Organizations: Retrieve organization details.
- Contacts: Access contact information.
- Bills: Manage and track bills.
- Estimates: Create and manage estimates.
- Items: Inventory management.
- Invoices: Generate and manage invoices.
- Expenses: Track and manage expenses.
- Credit Notes: Handle credit notes.
- Customer Payments: Manage customer payment records.
- Purchase Orders: Handle purchase order operations.
- Sales Orders: Manage sales orders.
- Journals: Access journal entries.
- Taxes: Manage tax information.
- Transactions: Track all transactions.

You can combine these endpoints to build pipelines that extract structured content from Zoho Books workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@zoho_books-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Zoho Books support.
    ```shell
    dlt init dlthub:zoho_books duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Zoho Books API, as specified in @zoho_books-docs.yaml 
    Start with endpoints "users" and "banking" and skip incremental loading for now. 
    Place the code in zoho_books_pipeline.py and name the pipeline zoho_books_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python zoho_books_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    OAuth 2.0 with refresh token flow is used, requiring client ID, client secret, and refresh token.
    
    To get appropriate API keys, please visit the original source at https://www.zoho.com/books/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python zoho_books_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline zoho_books load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset zoho_books_data
    The duckdb destination used duckdb:/zoho_books.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline zoho_books_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("zoho_books_pipeline").dataset()
   # get users table as Pandas frame
   data.users.df().head()
    ```

## Running into errors?

Requires the setup of an OAuth client in Zoho and ensuring the correct region code is used in the base URL. Possible errors include '401 Unauthorized' if OAuth credentials or token validity is an issue, '403 Forbidden' if there are incorrect permission scopes for the token, and '404 Not Found' if endpoint paths are incorrect.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)