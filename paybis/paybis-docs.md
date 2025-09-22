In this guide, we'll set up a complete Paybis data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def paybis_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://widget-api.sandbox.paybis.com/v2/public/currency/pairs",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "swap-crypto", "sell-crypto"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='paybis_pipeline',
        destination='duckdb',
        dataset_name='paybis_data', 
    )
    # Load the data
    load_info = pipeline.run(paybis_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from paybis’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Transactions**: Endpoints related to managing and retrieving transaction data, including creating and viewing transactions.
- **Currency Pairs**: Endpoints focused on the different currency pairs available for trading, including options for swapping, buying, and selling crypto.
- **Payment Methods**: Endpoints that provide lists of available payment methods and their limits, as well as specific methods related to certain currencies.
- **Payout Methods**: Endpoints for managing and retrieving information related to payout methods for specific currencies.
- **Requests**: Endpoints for creating, viewing, and managing requests, including specific request IDs.
- **Quotes**: Endpoints that provide public and private quote information for various transactions.
- **User Information**: Endpoints related to retrieving user-specific information based on a partner user ID.
- **Sumsub Integration**: Endpoints used for obtaining shared tokens for integration with Sumsub services.
- **Version 1 Endpoints**: General endpoints that may cover various functionalities under version 1 of the API.

You will then debug the Paybis pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Paybis support.
    ```shell
    dlt init dlthub:paybis duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Paybis API, as specified in @paybis-docs.yaml 
    Start with endpoints swap-crypto and sell-crypto and skip incremental loading for now. 
    Place the code in paybis_pipeline.py and name the pipeline paybis_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python paybis_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    Please use the sandbox API key, which will be provided during onboarding, to access the sandbox API at the URL <https://wallets-api.sandbox.paybis>, and if you are using the Shared KYC solution, you can call the **v1/sumsub/shared-token** endpoint to retrieve the Shared Access Token for approved applicants.
    
    To get the appropriate API keys, please visit the original source at https://docs.payb.is/reference/supported-assets-at-sandbox.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python paybis_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline paybis load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset paybis_data
    The duckdb destination used duckdb:/paybis.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline paybis_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("paybis_pipeline").dataset()
   # get "swap-crypto" table as Pandas frame
   data."swap-crypto".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)