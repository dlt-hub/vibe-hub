In this guide, we'll set up a complete API Ninjas data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def api_ninjas_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.api-ninjas.com/v1/",
            "auth": {
                "type": "apikey",
                "token": access_token,
            },
        },
        "resources": [
            "caloriesburned",
            "dogs",
            "goldprice"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='api_ninjas_pipeline',
        destination='duckdb',
        dataset_name='api_ninjas_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(api_ninjas_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from api_ninjas’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Health & Fitness: Includes endpoints like calories burned, calories burned activities, providing information on energy expenditure for various activities.
- Pets: Fetch data about dogs, including details like breed specifics, height, weight, and life expectancy.
- Finance: Access endpoints for gold prices, current and historical interest rates, providing financial market insights.
- Miscellaneous: Includes diverse endpoints like web scraping, generating QR codes, and joke fetching, suitable for varied use cases.

You can combine these endpoints to build pipelines that extract structured content from API Ninjas workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@api_ninjas-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with API Ninjas support.
    ```shell
    dlt init dlthub:api_ninjas duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@api_ninjas-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for API Ninjas API, as specified in @api_ninjas-docs.yaml 
    Start with endpoints "caloriesburned" and "dogs" and skip incremental loading for now. 
    Place the code in api_ninjas_pipeline.py and name the pipeline api_ninjas_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python api_ninjas_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via API keys. The key needs to be included in the header of each request under the 'X-Api-Key' field. An OAuth2 flow with a refresh token is also available for more secure and sustained authentication.
    
    To get appropriate API keys, please visit the original source at https://www.api-ninjas.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python api_ninjas_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline api_ninjas load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset api_ninjas_data
    The duckdb destination used duckdb:/api_ninjas.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline api_ninjas_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("api_ninjas_pipeline").dataset()
   # get "caloriesburned" table as Pandas frame
   data."caloriesburned".df().head()
    ```

## Running into errors?

API usage is subject to various restrictions including rate limits (e.g., 100 API calls per hour for basic plans), data limitations (maximum data return size of 2MB), and plan-based usage rights (e.g., commercial use only allowed under paid plans). Errors like '401 Unauthorized' indicate issues with API key validation or token expiry, and '404 Not Found' suggests invalid endpoint calls.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)