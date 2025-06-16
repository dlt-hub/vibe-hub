# How to load CoinAPI data in Python using dlt

**Build a CoinAPI-to-database or-dataframe pipeline in Python using dlt with automatic cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def coin_api_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.coinapi.io",
            "auth": {
    "type": "api_key",
    "token": access_token,
},
        },
        "resources": [
            "/v1/options/:exchange_id/current",
"/v1/quotes/current",
"/v1/orderbooks/:symbol_id/depth/current"
        ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='coin_api_pipeline',
        destination='duckdb',
        dataset_name='coin_api_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(coin_api_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from coin_api’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Current Data: Access current market data for exchanges, quotes, order books, and asset metrics.
- Historical Data: Fetch historical market data including OHLCV, asset metrics, and exchange metrics.
- Rates and Metrics: Obtain current and specific rates for assets, including exchange rates and timeseries data.

You can combine these endpoints to build pipelines that extract structured content from CoinAPI workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```python
    pip install dlt
    ```

    Initialize a dlt pipeline with CoinAPI support.
    ```
    dlt init dlthub:coin_api duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for CoinAPI API as specified in @coin_api-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in coin_api_pipeline.py and name the pipeline coin_api_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python coin_api_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@coin_api-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is managed through an API key included in the request headers. The API key should be sent as the value of the 'X-CoinAPI-Key' header in each request.

    In cursor, you would setup credentials in code as shown below:
    
    ```python
    def get_data() -> None:
        ...
        access_token = "my_access_token"
        ...
    ```
    
    If you want to protect these secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```
    python coin_api_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```python
    Pipeline coin_api load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset coin_api_data
    The duckdb destination used duckdb:/coin_api.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```python
    dlt pipeline coin_api_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("coin_api_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```
    

## Running into errors?

Notable considerations include: API key must be included in all requests; some responses might include nullable fields; data can be delayed and might change without notice; endpoint supports multiple response formats; rate limits can be easily exceeded leading to blocked access; ensure precise matching of request parameters to avoid bad requests.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)