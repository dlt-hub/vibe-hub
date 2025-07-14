In this guide, we'll set up a complete Watchmode data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def watchmode_data_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.watchmode.com/v1/",
            "auth": {
                "type": "apikey",
                "key": 'apiKey',
                "value": access_token
            },
        },
        "resources": [
            "sources",
            "titles",
            "person"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='watchmode_data_pipeline',
        destination='duckdb',
        dataset_name='watchmode_data_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(watchmode_data_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from watchmode_data’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Sources: Retrieve a list of available data sources. 
- Titles: Search and retrieve details for specific titles including movies and shows. 
- Person: Search and retrieve details about specific persons in the film and TV industry. 
- Genres: Fetch a list of genres. 
- Regions: Fetch a list of available regions. 
- Networks: Fetch a list of TV networks. 
- Search: Perform searches for titles based on various criteria. 
- Title Details: Retrieve detailed information about a title including cast, crew, and streaming availability.

You can combine these endpoints to build pipelines that extract structured content from Watchmode workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@watchmode_data-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Watchmode support.
    ```shell
    dlt init dlthub:watchmode_data duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Watchmode API, as specified in @watchmode_data-docs.yaml 
    Start with endpoints "sources" and "titles" and skip incremental loading for now. 
    Place the code in watchmode_data_pipeline.py and name the pipeline watchmode_data_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python watchmode_data_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is achieved via an API key included as a query parameter in each request. The API key is provided after registration and is necessary for accessing all endpoints.
    
    To get appropriate API keys, please visit the original source at https://www.watchmode.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python watchmode_data_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline watchmode_data load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset watchmode_data_data
    The duckdb destination used duckdb:/watchmode_data.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline watchmode_data_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("watchmode_data_pipeline").dataset()
   # get sources table as Pandas frame
   data.sources.df().head()
    ```

## Running into errors?

Data from the API can be cached for a maximum of 30 days after which it must be deleted or re-queried. Upon account cancellation, all cached data must be immediately deleted. The free plan is limited to data for the USA only, while paid plans provide more extensive data. The API strictly prohibits any form of reproduction, cloning, or modification of the data.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)