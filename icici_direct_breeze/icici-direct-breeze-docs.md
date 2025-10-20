In this guide, we'll set up a complete ICICI Direct Breeze data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def icici_direct_breeze_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.icicidirect.com/breezeapi/api/v1",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "dematholdings", "quotes"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='icici_direct_breeze_pipeline',
        destination='duckdb',
        dataset_name='icici_direct_breeze_data', 
    )
    # Load the data
    load_info = pipeline.run(icici_direct_breeze_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from icici_direct_breeze’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Order Management**: Endpoints related to placing, previewing, and managing orders.
  - `/breezeapi/api/v1/preview_order`: Previews an order before it is placed.
  - `/breezeapi/api/v1/order`: Manages the placement and processing of orders.
  - `/breezeapi/api/v1/squareoff`: Closes open positions in the market.

- **Customer and Account Information**: Endpoints that provide details about the customer and their accounts.
  - `/breezeapi/api/v1/customerdetails`: Retrieves customer-specific information.
  - `/breezeapi/api/v1/funds`: Provides information on available funds in the account.
  - `/breezeapi/api/v1/dematholdings`: Displays holdings in a demo account.

- **Market Data**: Endpoints for accessing market information and analysis.
  - `/breezeapi/api/v1/quotes`: Retrieves market quotes for various instruments.
  - `/breezeapi/api/v1/optionchain`: Provides data on available options for trading.
  - `/breezeapi/api/v1/historicalcharts`: Accesses historical price charts for analysis.

- **Trading Information**: Endpoints that give information on trades and portfolio holdings.
  - `/breezeapi/api/v1/trades`: Lists trades that have been executed.
  - `/breezeapi/api/v1/portfolioholdings`: Displays current holdings in the user's portfolio.

- **Order Types**: Endpoints specific to special order types.
  - `/breezeapi/api/v1/gttorder`: Manages Good Till Trigger (GTT) orders.

You will then debug the ICICI Direct Breeze pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with ICICI Direct Breeze support.
    ```shell
    dlt init dlthub:icici_direct_breeze duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for ICICI Direct Breeze API, as specified in @icici_direct_breeze-docs.yaml 
    Start with endpoints dematholdings and quotes and skip incremental loading for now. 
    Place the code in icici_direct_breeze_pipeline.py and name the pipeline icici_direct_breeze_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python icici_direct_breeze_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    You need to register to obtain an AppKey and a secret_key pair, which are required to connect to the server; the AppKey is the identity of the app for the API system, while the secret_key is used to encrypt messages sent to the API system. Additionally, all requests must contain an App Key, Secret Key, Session Token, Checksum, and Timestamp, and you must compute the Checksum using SHA256 hash (Time Stamp + JSON Post Data + secret_key). You can initiate the login flow by navigating to the Breeze API Login with your URL-encoded AppKey.
    
    To get the appropriate API keys, please visit the original source at https://api.icicidirect.com/breezeapi/documents/index.html.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python icici_direct_breeze_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline icici_direct_breeze load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset icici_direct_breeze_data
    The duckdb destination used duckdb:/icici_direct_breeze.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline icici_direct_breeze_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("icici_direct_breeze_pipeline").dataset()
   # get "dematholdings" table as Pandas frame
   data."dematholdings".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
