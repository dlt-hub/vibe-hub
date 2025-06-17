# How to load Recharge data in Python using dlt

**Build a Recharge-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def recharge_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.rechargeapps.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/checkouts/{checkout_id}",
            "/token_information",
            "/charges/{charge_id}/refund"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='recharge_pipeline',
        destination='duckdb',
        dataset_name='recharge_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(recharge_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from recharge’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Checkout Management: Provides endpoints for managing checkout processes, specifically for BigCommerce and Custom platforms, not Shopify.
- Charge Management: Handles operations related to customer charges, including listing charges, retrieving specific charges, and refunding charges.
- Address Management: Allows for retrieval and manipulation of customer addresses, including counting addresses and updating address details.
- Customer Management: Supports operations related to customers, such as listing customers and their associated details like addresses and payment methods.

You can combine these endpoints to build pipelines that extract structured content from Recharge workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Recharge support.
    ```shell
    dlt init dlthub:recharge duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Recharge API as specified in @recharge-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in recharge_pipeline.py and name the pipeline recharge_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python recharge_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@recharge-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication requires an API key passed in the header as 'X-Recharge-Access-Token'. The API supports OAuth2 with a refresh token flow.
    
    To get appropriate API keys, please visit the original source at https://developer.rechargepayments.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python recharge_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline recharge load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset recharge_data
    The duckdb destination used duckdb:/recharge.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline recharge_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("recharge_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

API does not support checkout operations for Shopify; use Shopify's system instead. Charges older than 90 days are not retrievable via API but accessible through the Exports tool. Be aware of the page-based pagination deprecation in favor of cursor-based pagination for better performance. The API requires specific headers for version control, and only supports certain payment processors like Stripe and Google Pay.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)