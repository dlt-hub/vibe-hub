In this guide, we'll set up a complete Square data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def square_bookings_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.squareup.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "list_bank_accounts",
            "get_bank_account",
            "app_submission"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='square_bookings_pipeline',
        destination='duckdb',
        dataset_name='square_bookings_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(square_bookings_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from square_bookings’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Bank Accounts: Manage bank accounts associated with a Square account.
- Bookings: Handle appointments and service bookings, including custom attributes for bookings.
- Customers: Manage customer profiles and related information.
- Catalog: Manage item catalogs, including categories, items, and pricing adjustments.

You can combine these endpoints to build pipelines that extract structured content from Square workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@square_bookings-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Square support.
    ```shell
    dlt init dlthub:square_bookings duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Square API, as specified in @square_bookings-docs.yaml 
    Start with endpoints "list_bank_accounts" and "get_bank_account" and skip incremental loading for now. 
    Place the code in square_bookings_pipeline.py and name the pipeline square_bookings_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python square_bookings_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses OAuth2 with a refresh token flow for authentication. Authentication requires specific permissions such as LOYALTY_READ and LOYALTY_WRITE for certain actions. The token URL is https://connect.squareup.com/oauth2/token.
    
    To get appropriate API keys, please visit the original source at https://squareup.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python square_bookings_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline square_bookings load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset square_bookings_data
    The duckdb destination used duckdb:/square_bookings.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline square_bookings_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("square_bookings_pipeline").dataset()
   # get list_bank_accounts table as Pandas frame
   data.list_bank_accounts.df().head()
    ```

## Running into errors?

Square API requires careful handling of permissions and versioning. It's important to manage OAuth tokens securely and ensure that they are refreshed before expiry. Square API versions update monthly, requiring potential code adjustments. Additionally, handling of null fields and error responses must be carefully managed to align with Square's API behavior.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)