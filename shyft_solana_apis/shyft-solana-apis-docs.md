In this guide, we'll set up a complete Shyft data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def shyft_solana_apis_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.shyft.to/sol/v1/marketplace",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "list_gasless", "update"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='shyft_solana_apis_pipeline',
        destination='duckdb',
        dataset_name='shyft_solana_apis_data', 
    )
    # Load the data
    load_info = pipeline.run(shyft_solana_apis_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from shyft_solana_apis’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Marketplace Endpoints**: Various functions related to managing listings, bids, and transactions in the marketplace.
  - `/sol/v1/marketplace/list_gasless`: List gasless marketplace items.
  - `/sol/v1/marketplace/update`: Update marketplace item details.
  - `/sol/v1/marketplace/cancel_bid`: Cancel an existing bid on an item.
  - `/sol/v1/marketplace/accept_bid`: Accept a bid on a marketplace item.
  - `/sol/v1/marketplace/withdraw_fee`: Withdraw fees associated with marketplace transactions.
  - `/sol/v1/marketplace/buy`: Purchase an item from the marketplace.
  - `/sol/v1/marketplace/create`: Create a new item listing in the marketplace.
  - `/sol/v1/marketplace/unlist`: Remove an item from the marketplace.
  - `/sol/v1/marketplace/bid`: Place a bid on a marketplace item.

- **Semi Wallet Endpoints**: Functions related to managing semi wallets.
  - `/sol/v1/semi_wallet/change_password`: Change the password for a semi wallet.
  - `/sol/v1/wallet/create_semi_wallet`: Create a new semi wallet.
  - `/sol/v1/semi_wallet/create`: Create a new semi wallet entry.

- **Storage Endpoints**: Used for uploading files or data.
  - `/sol/v1/storage/upload`: Upload files to storage.

- **Metadata Endpoints**: Functions related to metadata management.
  - `/sol/v1/metadata/create`: Create new metadata entries.

- **Token Endpoints**: Functions for managing tokens.
  - `/sol/v1/token/create_detach`: Create a token that can be detached.
  - `/sol/v1/token/create_from_metadata`: Create a token based on existing metadata.
  - `/sol/v1/token/burn_detach`: Burn a detached token.

You will then debug the Shyft pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - We suggest using a model like Claude 3.7 Sonnet or better
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started!

1. ⚙️ **Set up `dlt` Workspace**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt[workspace]
    ```

    Initialize a dlt pipeline with Shyft support.
    ```shell
    dlt init dlthub:shyft_solana_apis duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Shyft API, as specified in @shyft_solana_apis-docs.yaml 
    Start with endpoints list_gasless and update and skip incremental loading for now. 
    Place the code in shyft_solana_apis_pipeline.py and name the pipeline shyft_solana_apis_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python shyft_solana_apis_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate with the Shyft API, a key is required, which can be obtained by visiting <https://shyft.to/get-api-key>, and it should be applied in the headers using "x-api-key".
    
    To get the appropriate API keys, please visit the original source at https://docs.shyft.to/solana-apis/api-reference.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python shyft_solana_apis_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline shyft_solana_apis load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset shyft_solana_apis_data
    The duckdb destination used duckdb:/shyft_solana_apis.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline shyft_solana_apis_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("shyft_solana_apis_pipeline").dataset()
   # get "list_gasless" table as Pandas frame
   data."list_gasless".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)