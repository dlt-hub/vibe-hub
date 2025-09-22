In this guide, we'll set up a complete Etherscan data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def etherscan_accounts_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.etherscan.io/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "api?module=account&action=balance&address=0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae&tag=latest&apikey=YourApiKeyToken", "api?module=account&action=txlist&address=0xc5102fE9359FD9a28f877a67E36B0F050d81a3CC&startblock=0&endblock=99999999&page=1&offset=10&sort=asc&apikey=YourApiKeyToken"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='etherscan_accounts_pipeline',
        destination='duckdb',
        dataset_name='etherscan_accounts_data', 
    )
    # Load the data
    load_info = pipeline.run(etherscan_accounts_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from etherscan_accounts’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Account Transactions**: Endpoints for retrieving various types of transaction lists related to Ethereum accounts, including internal transactions and token transactions.
  - `txlistinternal`: Get internal transactions for a specific address.
  - `tokennfttx`: Get NFT token transactions for a specific address and contract.
  - `tokentx`: Get token transactions for a specific contract and address.
  
- **Account Balance**: Endpoints that provide balance information for Ethereum accounts.
  - `balance`: Get the current balance of a specific address.
  - `balancemulti`: Get balances for multiple addresses.
  - `balancehistory`: Fetch historical balance for a specific block number.

- **Mining Information**: Endpoints related to mined blocks for an Ethereum address.
  - `getminedblocks`: Retrieve mined blocks for a specific address.

- **Transaction History**: Endpoints for obtaining detailed transaction history information.
  - `txlist`: Get a list of transactions for a specific address.
  - `txlistinternal`: Get internal transaction details by transaction hash.

- **Contract Information**: Endpoints used to interact with smart contracts.
  - `getabi`: Retrieve the ABI (Application Binary Interface) for a specified contract address.

- **Beacon Withdrawal Transactions**: An endpoint to track withdrawal transactions from the Ethereum Beacon chain.
  - `txsBeaconWithdrawal`: Get withdrawal transactions for a specific address.

- **API Base Endpoints**: General access points for the API.
  - `/api`: Main API endpoint.
  - `/v2/api`: Versioned API endpoint for different functionalities.

You will then debug the Etherscan pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Etherscan support.
    ```shell
    dlt init dlthub:etherscan_accounts duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Etherscan API, as specified in @etherscan_accounts-docs.yaml 
    Start with endpoints api?module=account&action=balance&address=0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae&tag=latest&apikey=YourApiKeyToken and api?module=account&action=txlist&address=0xc5102fE9359FD9a28f877a67E36B0F050d81a3CC&startblock=0&endblock=99999999&page=1&offset=10&sort=asc&apikey=YourApiKeyToken and skip incremental loading for now. 
    Place the code in etherscan_accounts_pipeline.py and name the pipeline etherscan_accounts_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python etherscan_accounts_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To use the API, you need to create an account and obtain an API key, which can be acquired from the "Getting an API key" section, and you must include it as a query parameter labeled `apikey` in your requests.
    
    To get the appropriate API keys, please visit the original source at https://docs.etherscan.io/api-endpoints/accounts.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python etherscan_accounts_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline etherscan_accounts load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset etherscan_accounts_data
    The duckdb destination used duckdb:/etherscan_accounts.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline etherscan_accounts_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("etherscan_accounts_pipeline").dataset()
   # get "api?module=account&action=balance&address=0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae&tag=latest&apikey=YourApiKeyToken" table as Pandas frame
   data."api?module=account&action=balance&address=0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae&tag=latest&apikey=YourApiKeyToken".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)