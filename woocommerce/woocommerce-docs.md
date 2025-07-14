In this guide, we'll set up a complete WooCommerce data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def woocommerce_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.woocommerce.com/wp-json/wc/v3/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "store-api",
            "wp-admin",
            "wp-content"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='woocommerce_pipeline',
        destination='duckdb',
        dataset_name='woocommerce_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(woocommerce_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from woocommerce’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Store APIs: Provide access to store data and actions like product listings without requiring authentication.
- REST APIs: Allow interaction with site data through RESTful interfaces, including product updates and order management.
- Core Functionality: Access to core WordPress functionalities critical for site management.
- Media and Configuration: Endpoints for accessing and managing media files and site configurations.
- Debugging and Logs: Access to debugging logs and error tracking to maintain site health.
- Plugins and Themes: Manage and access plugins and themes directly through specific endpoints.

You can combine these endpoints to build pipelines that extract structured content from WooCommerce workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@woocommerce-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with WooCommerce support.
    ```shell
    dlt init dlthub:woocommerce duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for WooCommerce API, as specified in @woocommerce-docs.yaml 
    Start with endpoints "store-api" and "wp-admin" and skip incremental loading for now. 
    Place the code in woocommerce_pipeline.py and name the pipeline woocommerce_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python woocommerce_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is primarily done via API key. However, it is important to note that the Store API does not require authentication and is unauthenticated.
    
    To get appropriate API keys, please visit the original source at https://woocommerce.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python woocommerce_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline woocommerce load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset woocommerce_data
    The duckdb destination used duckdb:/woocommerce.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline woocommerce_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("woocommerce_pipeline").dataset()
   # get store-api table as Pandas frame
   data.store-api.df().head()
    ```

## Running into errors?

Users should be aware of the unauthenticated nature of the Store API, which does not provide access to sensitive data. It's also critical not to modify core directories like wp-admin or wp-includes, and to be cautious of phishing campaigns targeting WooCommerce stores. Additionally, there are specific nuances with WooCommerce extensions development and the need for appropriate setup with npm and wp-env for a local development environment.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)