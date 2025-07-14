In this guide, we'll set up a complete Yotpo data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def yotpo_reviews_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.yotpo.com/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "reviews",
            "coupons",
            "users"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='yotpo_reviews_pipeline',
        destination='duckdb',
        dataset_name='yotpo_reviews_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(yotpo_reviews_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from yotpo_reviews’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Reviews: Manage and retrieve product reviews.
- Coupons: Handle coupon creation, deletion, and statistics.
- User Data: Manage and retrieve user data related to reviews and questions.

You can combine these endpoints to build pipelines that extract structured content from Yotpo workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@yotpo_reviews-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Yotpo support.
    ```shell
    dlt init dlthub:yotpo_reviews duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Yotpo API, as specified in @yotpo_reviews-docs.yaml 
    Start with endpoints "reviews" and "coupons" and skip incremental loading for now. 
    Place the code in yotpo_reviews_pipeline.py and name the pipeline yotpo_reviews_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python yotpo_reviews_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication uses OAuth2 with a refresh token flow. The client_id and client_secret are required, and tokens are obtained via a token URL. Authentication details are passed in the header with 'Authorization' as the header name.
    
    To get appropriate API keys, please visit the original source at https://www.yotpo.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python yotpo_reviews_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline yotpo_reviews load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset yotpo_reviews_data
    The duckdb destination used duckdb:/yotpo_reviews.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline yotpo_reviews_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("yotpo_reviews_pipeline").dataset()
   # get reviews table as Pandas frame
   data.reviews.df().head()
    ```

## Running into errors?

Data caching up to 3 hours can delay updates. Rate limits are stringent with a maximum of 5000 requests/minute per app key. Webhook capabilities are limited and must be enabled by contacting Yotpo directly. Metadata updates take about 6 hours to reflect.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)