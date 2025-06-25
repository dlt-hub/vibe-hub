In this guide, we'll set up a complete Katana MRP data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def katana_mrp_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.katanamrp.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "customers",
            "shipping_fee",
            "costs"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='katana_mrp_pipeline',
        destination='duckdb',
        dataset_name='katana_mrp_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(katana_mrp_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from katana_mrp’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Customers: Manage and retrieve customer information.
- Shipping Fees: Access details on shipping fees applicable to orders.
- Costs: Retrieve cost-related data for products and orders.
- Customer Addresses: Manage and retrieve addresses associated with customers.
- Variants: Access and manage product variants.
- Tax Rates: Retrieve information about applicable tax rates.
- Suppliers: Manage and retrieve supplier data.
- Stocktakes: Access and manage stocktakes to adjust inventory records.
- Stock Adjustments: Manage adjustments to stock quantities.
- Sales Orders: Handle sales orders from creation to fulfillment.
- Sales Order Fulfillments: Manage the fulfillment processes of sales orders.
- Sales Order Addresses: Manage and retrieve addresses related to sales orders.
- Recipes: Access and manage production recipes.
- Purchase Orders: Manage purchase orders for materials and products.
- Products: Manage and retrieve product information.
- Price Lists: Retrieve and manage different price lists.
- Inventory: Manage and monitor inventory levels.
- Locations: Manage and retrieve locations for inventory and operations.
- Manufacturing Orders: Manage and track manufacturing orders.
- Materials: Manage and access materials required for manufacturing.

You can combine these endpoints to build pipelines that extract structured content from Katana MRP workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@katana_mrp-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Katana MRP support.
    ```shell
    dlt init dlthub:katana_mrp duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Katana MRP API, as specified in @katana_mrp-docs.yaml 
    Start with endpoints "customers" and "shipping_fee" and skip incremental loading for now. 
    Place the code in katana_mrp_pipeline.py and name the pipeline katana_mrp_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python katana_mrp_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via OAuth 2.0 with refresh token capabilities. The client must use a client ID, client secret, and refresh token to obtain an access token for making API requests. The Authorization header is used to pass the Bearer token.
    
    To get appropriate API keys, please visit the original source at https://katanamrp.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python katana_mrp_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline katana_mrp load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset katana_mrp_data
    The duckdb destination used duckdb:/katana_mrp.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline katana_mrp_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("katana_mrp_pipeline").dataset()
   # get customers table as Pandas frame
   data.customers.df().head()
    ```

## Running into errors?

Requires an API key from a Katana account. Some endpoints may have limitations on data retrieval, and it's essential to handle 401 Unauthorized errors by checking API key validity or permissions and 429 Too Many Requests by implementing exponential backoff for retries.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)