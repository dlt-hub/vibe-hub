In this guide, we'll set up a complete Lightspeed Retail data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def lightspeed_retail_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://<<domain_prefix>>.retail.lightspeed.app/api/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "promotions",
            "fulfillments",
            "products"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='lightspeed_retail_pipeline',
        destination='duckdb',
        dataset_name='lightspeed_retail_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(lightspeed_retail_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from lightspeed_retail’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Promotions: Manage promotional pricing and discounts.
- Fulfillments: Handle order fulfillment processes, tracking, and updates.
- Products: Manage product details including creation, updates, and listing.
- Price Books: Deal with custom price settings for different customer segments or conditions.
- Variants: Handle product variants and their attributes.
- Consignments: Manage consignment products, statuses, and updates.
- Sales: Process sales transactions, returns, and actions related to sales.
- Webhooks: Set up and manage webhooks for event-driven updates.

You can combine these endpoints to build pipelines that extract structured content from Lightspeed Retail workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@lightspeed_retail-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Lightspeed Retail support.
    ```shell
    dlt init dlthub:lightspeed_retail duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@lightspeed_retail-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Lightspeed Retail API, as specified in @lightspeed_retail-docs.yaml 
    Start with endpoints "promotions" and "fulfillments" and skip incremental loading for now. 
    Place the code in lightspeed_retail_pipeline.py and name the pipeline lightspeed_retail_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python lightspeed_retail_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication requires OAuth2 with tokens obtained via an authorization code flow. Tokens are used in the Authorization header as Bearer tokens.
    
    To get appropriate API keys, please visit the original source at https://www.lightspeedhq.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python lightspeed_retail_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline lightspeed_retail load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset lightspeed_retail_data
    The duckdb destination used duckdb:/lightspeed_retail.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline lightspeed_retail_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("lightspeed_retail_pipeline").dataset()
   # get "promotions" table as Pandas frame
   data."promotions".df().head()
    ```

## Running into errors?

Be aware of rate limits and ensure that the API calls are throttled accordingly. Use the User-Agent header meaningfully. There are specific rules regarding the modification of consignment statuses and product updates. Endpoints may return different responses based on the version used, and some are sunsetting. Pay attention to the requirement of multipart/form-data for certain types of requests.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)