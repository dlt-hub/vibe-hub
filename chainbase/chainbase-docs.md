In this guide, we'll set up a complete Chainbase data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def chainbase_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.chainbase.online/v1/nft",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "transfers", "collection"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='chainbase_pipeline',
        destination='duckdb',
        dataset_name='chainbase_data', 
    )
    # Load the data
    load_info = pipeline.run(chainbase_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from chainbase’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Account Endpoints**: Access account-specific information such as ENS details and transaction history.
  - `/v1/account/ens?chain_id=${network_id}`: Retrieves the ENS information for a specified account.
  - `/v1/account/txs`: Lists transactions associated with a specific account.

- **Block Endpoints**: Provide details about blockchain blocks and their numbers.
  - `/v1/block/detail`: Returns detailed information about a specific block.
  - `/v1/block/number/latest`: Fetches the latest block number on the blockchain.

- **Transaction Endpoints**: Retrieve information on specific transactions.
  - `/v1/tx/detail`: Provides details for a specific transaction.

- **Contract Endpoints**: Interact with smart contracts on the blockchain.
  - `/v1/contract/call`: Executes a call to a smart contract.

- **NFT Endpoints**: Access various information related to non-fungible tokens (NFTs).
  - `/v1/nft/metadata`: Fetches metadata for a specific NFT.
  - `/v1/nft/owners`: Lists owners of a particular NFT.
  - `/v1/nft/owner`: Gets information about a specific NFT owner.
  - `/v1/nft/rarity`: Provides rarity details of NFTs.
  - `/v1/nft/owner/history`: Shows the ownership history of an NFT.
  - `/v1/nft/transfers`: Lists transfers of a specific NFT.
  - `/v1/nft/collection/items`: Retrieves items in a specific NFT collection.
  - `/v1/nft/collection`: Provides details about an NFT collection.

- **Token Endpoints**: Access information related to tokens including price, holders, and transaction history.
  - `/v1/token/transfers`: Lists transfers associated with a specific token.
  - `/v1/token/price/history`: Provides historical price data for a token.
  - `/v1/token/metadata`: Retrieves metadata for a specific token.
  - `/v1/token/top-holders`: Lists the top holders of a specific token.
  - `/v1/token/holders`: Provides information on token holders.
  - `/v1/token/price`: Fetches the current price of a token.

You will then debug the Chainbase pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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
    pip install "dlt[workspace]"
    ```

    Initialize a dlt pipeline with Chainbase support.
    ```shell
    dlt init dlthub:chainbase duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Chainbase API, as specified in @chainbase-docs.yaml 
    Start with endpoints transfers and collection and skip incremental loading for now. 
    Place the code in chainbase_pipeline.py and name the pipeline chainbase_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python chainbase_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To access the API, you need an API key, which you can create by going to [console.chainbase.com](https://console.chainbase.com/), and for demo purposes, you can use the key “demo” to test out the API.
    
    To get the appropriate API keys, please visit the original source at https://docs.chainbase.com/platform/features/api/chain-rpc.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python chainbase_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline chainbase load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset chainbase_data
    The duckdb destination used duckdb:/chainbase.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline chainbase_pipeline show
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("chainbase_pipeline").dataset()
   # get "transfers" table as Pandas frame
   data."transfers".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
