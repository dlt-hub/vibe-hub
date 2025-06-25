In this guide, we'll set up a complete Mercado Ads data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def mercado_ads_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.mercado-ads.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "brand_advertisers",
            "display_campaigns",
            "product_items"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='mercado_ads_pipeline',
        destination='duckdb',
        dataset_name='mercado_ads_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(mercado_ads_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from mercado_ads’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Brand Management: Includes endpoints for advertisers, campaigns, items, keywords, and their respective metrics under the brand category.
- Display Advertising: Covers advertisers, campaigns, line items, creatives, and their metrics for display advertising.
- Product Advertising: Involves advertisers, campaigns, items, and their metrics specifically for product-related advertisements.

You can combine these endpoints to build pipelines that extract structured content from Mercado Ads workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@mercado_ads-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Mercado Ads support.
    ```shell
    dlt init dlthub:mercado_ads duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Mercado Ads API, as specified in @mercado_ads-docs.yaml 
    Start with endpoints "brand_advertisers" and "display_campaigns" and skip incremental loading for now. 
    Place the code in mercado_ads_pipeline.py and name the pipeline mercado_ads_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python mercado_ads_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses OAuth2 for authentication. The flow involves obtaining a refresh token, which is then used to acquire access tokens from a designated token URL. The client_id, client_secret, and refresh_token are required for this process, and the Authorization header is used to include the token in API requests.
    
    To get appropriate API keys, please visit the original source at https://www.mercadolibre.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python mercado_ads_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline mercado_ads load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset mercado_ads_data
    The duckdb destination used duckdb:/mercado_ads.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline mercado_ads_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("mercado_ads_pipeline").dataset()
   # get brand_advertisers table as Pandas frame
   data.brand_advertisers.df().head()
    ```

## Running into errors?

Endpoints require parameters like lookback_days, start_date, and end_date, with specific restrictions based on the ad type. Error handling must account for potential HTTP statuses 400 (Bad Request), 401 (Unauthorized), and 429 (Too Many Requests), implying a need for input validation and careful management of request rate.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)