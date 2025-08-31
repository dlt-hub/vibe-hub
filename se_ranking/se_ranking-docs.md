In this guide, we'll set up a complete SE Ranking data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def se_ranking_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.api-name.com/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "statistics",
            "customer/content",
            "domain/overview"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='se_ranking_pipeline',
        destination='duckdb',
        dataset_name='se_ranking_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(se_ranking_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from se_ranking’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Domain Analysis: Provides endpoints for retrieving data about domains, including overview, competitors, and keyword analysis.
- Keywords Data: Offers endpoints for exporting keywords, and obtaining organic and paid keywords statistics.
- Backlinks: Endpoints related to retrieving and analyzing backlinks data, including history and export functionalities.
- Statistics: Allows for the aggregation and retrieval of statistics related to customer interactions and content.

You can combine these endpoints to build pipelines that extract structured content from SE Ranking workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@se_ranking-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with SE Ranking support.
    ```shell
    dlt init dlthub:se_ranking duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@se_ranking-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for SE Ranking API, as specified in @se_ranking-docs.yaml 
    Start with endpoints "statistics" and "customer/content" and skip incremental loading for now. 
    Place the code in se_ranking_pipeline.py and name the pipeline se_ranking_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python se_ranking_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via OAuth 2.0 with an API key required, which must be generated from the SE Ranking user account. The API key is included in the Authorization HTTP header or as a token query parameter.
    
    To get appropriate API keys, please visit the original source at https://seranking.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python se_ranking_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline se_ranking load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset se_ranking_data
    The duckdb destination used duckdb:/se_ranking.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline se_ranking_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("se_ranking_pipeline").dataset()
   # get "statistics" table as Pandas frame
   data."statistics".df().head()
    ```

## Running into errors?

The API key is crucial for all requests and should be securely stored. Users must handle rate limits carefully to avoid exceeding the request limit. Also, using the API consumes limits similar to the platform's tool, so careful planning of API usage is necessary to avoid unexpected depletion of resources.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)