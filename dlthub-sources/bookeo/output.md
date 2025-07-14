In this guide, we'll set up a complete Bookeo data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def bookeo_api_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.bookeo.com/v2/",
            "auth": {
                "type": "apikey",
                "key": access_token,
            },
        },
        "resources": [
            "settings/products",
            "availability/slots",
            "bookings"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='bookeo_api_pipeline',
        destination='duckdb',
        dataset_name='bookeo_api_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(bookeo_api_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from bookeo_api’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Settings: Manage product settings and language settings.
- Availability: Check available slots and matching slots for bookings.
- Bookings: Create new bookings.
- Webhooks: Manage webhook settings and responses.

You can combine these endpoints to build pipelines that extract structured content from Bookeo workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@bookeo_api-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Bookeo support.
    ```shell
    dlt init dlthub:bookeo_api duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@bookeo_api-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Bookeo API, as specified in @bookeo_api-docs.yaml 
    Start with endpoints "settings/products" and "availability/slots" and skip incremental loading for now. 
    Place the code in bookeo_api_pipeline.py and name the pipeline bookeo_api_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python bookeo_api_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is provided via API Key included in the header of each request. The 'Authorization' header is used to pass the API key.
    
    To get appropriate API keys, please visit the original source at https://www.bookeo.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python bookeo_api_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline bookeo_api load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset bookeo_api_data
    The duckdb destination used duckdb:/bookeo_api.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline bookeo_api_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("bookeo_api_pipeline").dataset()
   # get "settings/products" table as Pandas frame
   data."settings/products".df().head()
    ```

## Running into errors?

Bookeo does not store credit card details. It's important to handle webhooks carefully as they are delivered in sequence and unresponsive webhooks can be blocked after 24 hours. Be aware of rate limits that may result in 429 errors and require handling retries based on the 'retryAfter' value provided.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)