In this guide, we'll set up a complete Stacks data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def stacks_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "http://localhost:20443/v2/clarity",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "marf", "metadata"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='stacks_pipeline',
        destination='duckdb',
        dataset_name='stacks_data', 
    )
    # Load the data
    load_info = pipeline.run(stacks_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from stacks’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Mempool**: Endpoints related to querying unconfirmed transactions and the state of the mempool.
  - `/v2/mempool/query`: Retrieve information from the mempool.
  
- **Attachments**: Endpoints dealing with data attachments associated with transactions or blocks.
  - `/v2/attachments/{hash}`: Access specific attachment by its hash.
  - `/v2/attachments/inv`: Invalidate an attachment.

- **Microblocks**: Endpoints for managing and querying microblocks.
  - `/v2/microblocks/{microblock_id}`: Get details for a specific microblock.
  - `/v2/microblocks/confirmed/{block_id}`: Retrieve confirmed microblock information.

- **Transactions**: Endpoints related to transaction management and querying.
  - `/v2/transactions/unconfirmed/{txid}`: Check the status of an unconfirmed transaction.
  - `/v3/transaction/{txid}`: Retrieve transaction details by ID.

- **Blocks**: Endpoints for block proposals and block management.
  - `/v3/block_proposal`: Submit a proposal for a new block.
  - `/v3/blocks/upload`: Upload a new block.

- **Health**: Check the health status of the service.
  - `/v3/health`: Get health status of the server.

- **Clarity**: Interact with Clarity smart contracts and related operations.
  - `/v2/clarity/marf/{marf_key_hash}`: Access MARF data using a specific key hash.

- **StackerDB**: Endpoints for accessing StackerDB functionalities.
  - `/v2/stackerdb/{principal}`: Query StackerDB using a principal.

- **Tenures**: Manage and retrieve information about consensus tenures.
  - `/v3/tenures/tip/{consensus_hash}`: Get information about the current tenure tip.

- **Headers**: Interact with block headers.
  - `/v2/headers/{quantity}`: Retrieve a specified number of block headers.

- **Neighbors**: Manage network neighbor information.
  - `/v2/neighbors`: Get a list of neighboring nodes.

- **Contracts**: Interact with smart contract functions.
  - `/v2/contracts/call-read/{contract_address}`: Execute a read-only call on a smart contract.

- **Sortitions**: Endpoints related to sortition processes.
  - `/v3/sortitions/burn/{burn_header_hash}`: Manage sortition burns using header hash.

You will then debug the Stacks pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Stacks support.
    ```shell
    dlt init dlthub:stacks duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Stacks API, as specified in @stacks-docs.yaml 
    Start with endpoints marf and metadata and skip incremental loading for now. 
    Place the code in stacks_pipeline.py and name the pipeline stacks_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python stacks_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate, you must include an `Authorization` header when the query parameter `broadcast=1` is supplied, where the value should be `rpcAuth`, which you need to obtain from the relevant documentation or API provider.
    
    To get the appropriate API keys, please visit the original source at https://docs.stacks.co/reference/api.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python stacks_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline stacks load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset stacks_data
    The duckdb destination used duckdb:/stacks.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline stacks_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("stacks_pipeline").dataset()
   # get "marf" table as Pandas frame
   data."marf".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)