# How to load PayPal data in Python using dlt

**Build a PayPal-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def paypal_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api-m.sandbox.paypal.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/v2/checkout/orders",
            "/v2/payments/captures/{capture_id}/refund",
            "/reports"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='paypal_pipeline',
        destination='duckdb',
        dataset_name='paypal_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(paypal_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from paypal’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Transactions: Manage and submit transactions.
- Referral Commissions: Get commissions for referring merchants to PayPal.
- Checkout Processes: Different types of checkout integrations such as PayPal Checkout, Expanded, and Enterprise.
- Dispute Management: Handle disputes, send messages, provide evidence, and manage claims.
- Reporting: Access and generate reports related to transactions and activities.
- Donation Management: Create and manage donation buttons, render SDKs, and access donation transaction information.

You can combine these endpoints to build pipelines that extract structured content from PayPal workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with PayPal support.
    ```shell
    dlt init dlthub:paypal duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for PayPal API as specified in @paypal-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in paypal_pipeline.py and name the pipeline paypal_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python paypal_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@paypal-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Uses OAuth 2.0 for authentication. Access tokens are required for making API requests which are obtained using the client credentials flow.
    
    To get appropriate API keys, please visit the original source at https://developer.paypal.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python paypal_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline paypal load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset paypal_data
    The duckdb destination used duckdb:/paypal.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline paypal_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("paypal_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Rate limiting may apply; be mindful of PCI DSS compliance requirements when handling payment information. Ensure correct usage as per PayPal's Acceptable Use Policy and legal requirements. Use SANDBOX for development and testing, PRODUCTION for deployment.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)