In this guide, we'll set up a complete EveryPay data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def everypay_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.everypay.gr/customers/cus_RxE6DEcITxOYyynErDK6VoBa/cards",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "crd_FIAoNSiXlwpnSJrfojsrPdH4", "crd_xDQhOiVCyKFcUphQMEdUPuXQ"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='everypay_pipeline',
        destination='duckdb',
        dataset_name='everypay_data', 
    )
    # Load the data
    load_info = pipeline.run(everypay_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from everypay’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Sellers**: Endpoints related to seller information, including balance and transfers.
  - `/sellers/{sellerId}`: Retrieve information about a specific seller.
  - `/sellers/sel_lkPJrywRlDAp5oKrqOeRHEt0/balance`: Get the balance of a specific seller.
  - `/sellers/sel_U0FBPmz5t2zjDjKaOGABlo6A/transfers`: Get transfer information related to a specific seller.

- **Transfers**: Manage and retrieve information about funds transferred between accounts.
  - `/transfers`: List all transfers.
  - `/transfers/{transferId}`: Get details of a specific transfer.
  - `/transfers/tra_lkPJrywRlDAp5oKrqOeRHEt0`: Retrieve information about a specific transfer using its ID.

- **Customers**: Endpoints related to customer accounts and their cards.
  - `/customers/{customerId}`: Retrieve information about a specific customer.
  - `/customers/cus_RxE6DEcITxOYyynErDK6VoBa`: Get details of a specific customer.
  - `/customers/cus_RxE6DEcITxOYyynErDK6VoBa/cards`: List all cards associated with a specific customer.
  - `/customers/cus_RxE6DEcITxOYyynErDK6VoBa/cards/crd_FIAoNSiXlwpnSJrfojsrPdH4`: Details of a specific card for a customer.
  - `/customers/cus_ZKTziKqElUcg0fLUNkfA0J5d/mandates/man_saKM36D59XehC9IEr1F3QgUW`: Get mandate details for a specific customer.

- **Payments**: Endpoints to manage and retrieve payment transactions.
  - `/payments`: List payments with pagination options like count and offset.
  - `/payments/pmt_lkPJrywRlDAp5oKrqOeRHEt0`: Get details of a specific payment.

- **Refunds**: Endpoints related to processing refunds.
  - `/refunds`: List all refunds.
  - `/refunds/{refundId}`: Get details of a specific refund.
  - `/refunds/ref_ew6fZqrUbWrfCQuPgcSjeYD7`: Retrieve information about a specific refund using its ID.

- **Notifications**: Manage notifications related to transactions and events.
  - `/notifications/{notificationId}`: Retrieve details of a specific notification.

- **Tokens**: Endpoints for managing tokens used for secure transactions.
  - `/tokens/ctn_KJ53Qx3ZGaNLttUNxyB5JQO3`: Get details of a specific token.

- **Reversals**: Manage transaction reversals.
  - `/reversals`: List all reversals.

You will then debug the EveryPay pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with EveryPay support.
    ```shell
    dlt init dlthub:everypay duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for EveryPay API, as specified in @everypay-docs.yaml 
    Start with endpoints crd_FIAoNSiXlwpnSJrfojsrPdH4 and crd_xDQhOiVCyKFcUphQMEdUPuXQ and skip incremental loading for now. 
    Place the code in everypay_pipeline.py and name the pipeline everypay_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python everypay_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate with the EveryPay API, you need a key which is your secret key (e.g., sk_PqSohnrYrR) that should be applied in the authorization header using Basic Auth format.
    
    To get the appropriate API keys, please visit the original source at https://docs.everypay.gr/tools/api-reference/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python everypay_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline everypay load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset everypay_data
    The duckdb destination used duckdb:/everypay.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline everypay_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("everypay_pipeline").dataset()
   # get "crd_FIAoNSiXlwpnSJrfojsrPdH4" table as Pandas frame
   data."crd_FIAoNSiXlwpnSJrfojsrPdH4".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)