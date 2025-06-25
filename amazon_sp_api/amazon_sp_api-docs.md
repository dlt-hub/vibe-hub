In this guide, we'll set up a complete Amazon SP-API data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def amazon_sp_api_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.amazonservices.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "custom_reports",
            "private_seller_applications",
            "public_seller_applications"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='amazon_sp_api_pipeline',
        destination='duckdb',
        dataset_name='amazon_sp_api_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(amazon_sp_api_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from amazon_sp_api’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Reports Management: Includes endpoints like custom reports and general reports API for generating and managing reports.
- Seller Applications: Manages both private and public seller applications, providing separate endpoints for each.
- Vendor Applications: Similar to seller applications but specifically for vendors, with private access.
- Order Management: Handles order operations including B2B and Multi-Channel Fulfillment (MCF).
- Product Data Management: Manages product-related data including pricing and inventory.
- Notification Services: Provides access to AnyOfferChangeNotification (AOCN) for offer updates.

You can combine these endpoints to build pipelines that extract structured content from Amazon SP-API workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@amazon_sp_api-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Amazon SP-API support.
    ```shell
    dlt init dlthub:amazon_sp_api duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Amazon SP-API API, as specified in @amazon_sp_api-docs.yaml 
    Start with endpoints "custom_reports" and "private_seller_applications" and skip incremental loading for now. 
    Place the code in amazon_sp_api_pipeline.py and name the pipeline amazon_sp_api_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python amazon_sp_api_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is handled through OAuth2, requiring an access token to make API requests.
    
    To get appropriate API keys, please visit the original source at https://developer.amazon.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python amazon_sp_api_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline amazon_sp_api load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset amazon_sp_api_data
    The duckdb destination used duckdb:/amazon_sp_api.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline amazon_sp_api_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("amazon_sp_api_pipeline").dataset()
   # get custom_reports table as Pandas frame
   data.custom_reports.df().head()
    ```

## Running into errors?

Developers need to ensure migration from MWS to SP-API as per Amazon's guidelines. OAuth permissions and token validity must be carefully managed. Also, compliance with Amazon's security standards is mandatory, and developers should leverage the provided sandbox environments for testing before production deployment.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)