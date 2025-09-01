In this guide, we'll set up a complete Criteo data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def criteo_marketing_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.criteo.com/2023-07/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "assets/upload",
            "creatives",
            "categories"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='criteo_marketing_pipeline',
        destination='duckdb',
        dataset_name='criteo_marketing_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(criteo_marketing_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from criteo_marketing’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Asset Management: Allows uploading and managing creative assets like images.
- Category Management: Enables fetching and managing product categories associated with retailers.
- Creative Management: Focuses on creating, retrieving, updating, and managing advertising creatives.
- Line Item Management: Manages product SKUs within line items and collections, including creation, updating, and deletion.
- Reporting: Facilitates the creation of campaign and line-item reports, crucial for performance analysis.

You can combine these endpoints to build pipelines that extract structured content from Criteo workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@criteo_marketing-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Criteo support.
    ```shell
    dlt init dlthub:criteo_marketing duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@criteo_marketing-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Criteo API, as specified in @criteo_marketing-docs.yaml 
    Start with endpoints "assets/upload" and "creatives" and skip incremental loading for now. 
    Place the code in criteo_marketing_pipeline.py and name the pipeline criteo_marketing_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python criteo_marketing_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The Criteo API uses OAuth2 for authentication. The token is obtained from a specified URL and must be included in the Authorization header as a Bearer token for all API requests.
    
    To get appropriate API keys, please visit the original source at https://www.criteo.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python criteo_marketing_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline criteo_marketing load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset criteo_marketing_data
    The duckdb destination used duckdb:/criteo_marketing.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline criteo_marketing_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("criteo_marketing_pipeline").dataset()
   # get "assets/upload" table as Pandas frame
   data."assets/upload".df().head()
    ```

## Running into errors?

The API has a rate limit of 100 requests per minute per app. New updates might introduce breaking changes requiring client-side updates. Some endpoints are version sensitive and subject to specific rate limits. The Preview version should not be used in production as it may include unstable changes.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)