# How to load Retail Express data in Python using dlt

**Build a Retail Express-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def retail_express_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "/v2.1",
            "auth": {
                "type": "apikey",
                "token": access_token
            },
        },
        "resources": [
            "/v2.1/customers.json",
            "/v2.1/orders.json",
            "/v2.1/products.json"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='retail_express_pipeline',
        destination='duckdb',
        dataset_name='retail_express_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(retail_express_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from retail_express’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Customers: Retrieve information about customers, including specific customer details. Supports operations like GET, POST, PUT, and DELETE.
- Orders: Fetch orders and specific order details by ID using the GET method.
- Products: Access product data, including details of specific products by ID. Allows searching via parameters like SKU.
- Outlets: Manage outlet information and retrieve details about specific outlets.

You can combine these endpoints to build pipelines that extract structured content from Retail Express workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Retail Express support.
    ```shell
    dlt init dlthub:retail_express duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Retail Express API as specified in @retail_express-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in retail_express_pipeline.py and name the pipeline retail_express_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python retail_express_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@retail_express-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication requires an API key placed in the header of the request. The header name should be 'x-api-key'. Additionally, access tokens are used which expire after 60 minutes, necessitating re-authentication.
    
    To get appropriate API keys, please visit the original source at https://developer.retailexpress.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python retail_express_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline retail_express load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset retail_express_data
    The duckdb destination used duckdb:/retail_express.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline retail_express_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("retail_express_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

API keys are unique and must be requested from each client intending to use the API. Access tokens expire after 60 minutes, which means frequent re-authentication may be necessary. Attention should be paid to handling rate limits and potential errors such as unauthorized access or server unavailability.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)