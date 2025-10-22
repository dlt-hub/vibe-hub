In this guide, we'll set up a complete Eway data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def eway_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.sandbox.ewaypayments.com/Transaction",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "11259580", "InvoiceNumber"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='eway_pipeline',
        destination='duckdb',
        dataset_name='eway_data', 
    )
    # Load the data
    load_info = pipeline.run(eway_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from eway’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Payment Processing**: Endpoints related to direct payment transactions and processing.
  - `/DirectPayment.json`: Handles direct payment submissions.
  - `/DirectPayment.xml`: XML version for direct payment submissions.

- **Customer Management**: Endpoints for customer-related operations.
  - `/Customer/?Filter_TokenCustomerID={TokenCustomerID}`: Retrieves customer information based on a token.
  - `/DirectCustomerSearch.json`: Searches for customers directly.

- **Access Codes**: Endpoints for creating and managing access codes.
  - `/CreateAccessCodeShared.xml`: Creates a shared access code in XML format.
  - `/AccessCodes`: Retrieves a list of access codes.
  - `/GetAccessCodeResult.json`: Gets the result of a specific access code request.
  - `/GetAccessCodeResult.xml`: XML version for retrieving access code results.
  - `/AccessCode/{AccessCode}`: Access specific access code details.

- **Transaction Management**: Endpoints for handling transactions.
  - `/Transaction/44DD7aVwPYUPemGRf7pcWxyX2FJS-0Wk7xr9iE7Vatk_5vJimEbHveGSqX52B00QsBXqbLh9mGZxMHcjThQ_ITsCZ3JxKOY88WOVsFTLPrGtHRkK0E9ZDVh_Wz326QZlNlwx2`: Accesses details of a specific transaction.
  - `/Transaction/InvoiceRef/{Invoice}`: Retrieves transaction details using an invoice reference.
  - `/TransactionSearch.json`: Searches for transactions based on specific criteria.

- **Settlement Reports**: Endpoints for querying settlement reports.
  - `/Search/Settlement?SettlementDate=2015-02-02&ReportMode=Both`: Retrieves settlement reports for a specific date.
  - `/Search/Settlement?SettlementDate=2015-02-02&ReportMode=Both&CardType=VI&Currency=AUD`: Searches settlement reports with additional filters.

- **Security and Encryption**: Endpoints for security measures.
  - `/encrypt`: Endpoint for handling encryption processes.
  - `/3dsecure/75593848?AccessCode=44DD76JCBUSOCQo7YkWHxe5bBgfD2zxow_ylTYIxWxzjavmXsTt3QBuf8Wew6kcy4uo5RooAywCcIaYL_nRidwmp5BW3MHotKSPNX68KM4XVm6XgyUcjwmHUevvRIsGgxJ7UW`: Handles 3D secure transactions with an access code.

- **SOAP Services**: Endpoints related to SOAP web services.
  - `/soap.asmx?WSDL`: Provides the WSDL for SOAP services.
  - `/soap.asmx`: Accesses the SOAP service directly.

- **XML-RPC Services**: Endpoint for XML-RPC functionality.
  - `/xml-rpc`: Supports XML-RPC calls for remote procedure calls.

You will then debug the Eway pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Eway support.
    ```shell
    dlt init dlthub:eway duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Eway API, as specified in @eway-docs.yaml 
    Start with endpoints 11259580 and InvoiceNumber and skip incremental loading for now. 
    Place the code in eway_pipeline.py and name the pipeline eway_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python eway_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate with Eway's Rapid API, you need an API Key and a Password, which are used in basic authentication; these should be included in the username and password fields respectively, and sent in the HTTP header when making requests.
    
    To get the appropriate API keys, please visit the original source at https://eway.io/api-v3/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python eway_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline eway load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset eway_data
    The duckdb destination used duckdb:/eway.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline eway_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("eway_pipeline").dataset()
   # get "11259580" table as Pandas frame
   data."11259580".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
