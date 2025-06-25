In this guide, we'll set up a complete Cin7 data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def cin7_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.cin7.com/v1/",
            "auth": {
                "type": "apikey",
                "token": access_token,
            },
        },
        "resources": [
            "bank_accounts",
            "attribute_sets",
            "accounts"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='cin7_pipeline',
        destination='duckdb',
        dataset_name='cin7_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(cin7_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from cin7’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Financial Transactions: Access and manage bank account transactions.
- Product Management: Manage attributes, products, brands, and product families.
- Sales and Customers: Handle information related to customers, deals, and sales listings.
- Logistics: Manage suppliers, carriers, and locations.
- Purchasing: Oversee purchase orders and transactions.

You can combine these endpoints to build pipelines that extract structured content from Cin7 workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@cin7-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Cin7 support.
    ```shell
    dlt init dlthub:cin7 duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Cin7 API, as specified in @cin7-docs.yaml 
    Start with endpoints "bank_accounts" and "attribute_sets" and skip incremental loading for now. 
    Place the code in cin7_pipeline.py and name the pipeline cin7_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python cin7_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed through an API key and requires an AccountID. Both must be included in headers for all requests.
    
    To get appropriate API keys, please visit the original source at https://www.cin7.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python cin7_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline cin7 load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset cin7_data
    The duckdb destination used duckdb:/cin7.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline cin7_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("cin7_pipeline").dataset()
   # get bank_accounts table as Pandas frame
   data.bank_accounts.df().head()
    ```

## Running into errors?

Ensure correct usage of AccountID and API Key as incorrect credentials lead to 401 Unauthorized errors. Be cautious of the API's rate limits to avoid 429 Too Many Requests errors. Also, verify endpoint paths and parameters to prevent 404 Not Found errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)