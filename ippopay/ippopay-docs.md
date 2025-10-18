In this guide, we'll set up a complete IppoPay data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def ippopay_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://public_key:secret_key@api.ippopay.com/v1/payout/transfer",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "request`", "detail"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='ippopay_pipeline',
        destination='duckdb',
        dataset_name='ippopay_data', 
    )
    # Load the data
    load_info = pipeline.run(ippopay_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from ippopay’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Order Endpoints**: 
  - `/v1/order/ORDER_ID`: Retrieve details for a specific order using its ID.
  - `/v1/order/ORDER_ID/transaction`: Get transaction details related to a specific order.

- **Payout Endpoints**: 
  - `/v1/payout/account/balance`: Check the balance of the payout account.
  - `/v1/payout/beneficiary/list`: Retrieve a list of beneficiaries associated with the payout account.
  - `/v1/payout/beneficiary/getdetails/{beneficiary_ref}`: Get detailed information about a specific beneficiary using their reference.
  - `/v1/payout/beneficiary/create`: Create a new beneficiary for future payouts.
  - `/v1/payout/transfer/detail?trans_ref=xxxx`: Get details about a specific transfer using its reference.
  - `/v1/payout/transfer/request`: Request a payout transfer to a beneficiary.

You will then debug the IppoPay pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with IppoPay support.
    ```shell
    dlt init dlthub:ippopay duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for IppoPay API, as specified in @ippopay-docs.yaml 
    Start with endpoints request` and detail and skip incremental loading for now. 
    Place the code in ippopay_pipeline.py and name the pipeline ippopay_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python ippopay_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To use the Ippopay payment gateway, you need a public key and a secret key, which can be obtained from the API dashboard at https://app.ippopay.com/settings/api, and you should include them in the URL as `https://public_key:secret_key@api.ippopay.com` when making API requests.
    
    To get the appropriate API keys, please visit the original source at https://docs.ippopay.com/server-side-integrations/rest-api.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python ippopay_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline ippopay load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset ippopay_data
    The duckdb destination used duckdb:/ippopay.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline ippopay_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("ippopay_pipeline").dataset()
   # get "request`" table as Pandas frame
   data."request`".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
