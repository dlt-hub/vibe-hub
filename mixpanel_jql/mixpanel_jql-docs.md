In this guide, we'll set up a complete Mixpanel data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def mixpanel_jql_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.mixpanel.com/docs/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "events",
            "people",
            "countEvents"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='mixpanel_jql_pipeline',
        destination='duckdb',
        dataset_name='mixpanel_jql_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(mixpanel_jql_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from mixpanel_jql’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Event Data: Allows posting of event data.
- User Data: For posting user profile information.
- Event Counting: Various endpoints for counting events, e.g., countEvents, countPeopleByAge.
- Data Aggregation: Endpoints for summarizing data, e.g., sumNotificationHits, numericSummaryPageLoadTimes.
- Advanced Queries: Includes more complex query capabilities like activity_feed and activity_stream_query.

You can combine these endpoints to build pipelines that extract structured content from Mixpanel workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@mixpanel_jql-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Mixpanel support.
    ```shell
    dlt init dlthub:mixpanel_jql duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Mixpanel API, as specified in @mixpanel_jql-docs.yaml 
    Start with endpoints "events" and "people" and skip incremental loading for now. 
    Place the code in mixpanel_jql_pipeline.py and name the pipeline mixpanel_jql_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python mixpanel_jql_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication requires a Service Account with the role of either admin or owner. The token is sent in the header under the 'Authorization' field.
    
    To get appropriate API keys, please visit the original source at https://www.mixpanel.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python mixpanel_jql_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline mixpanel_jql load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset mixpanel_jql_data
    The duckdb destination used duckdb:/mixpanel_jql.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline mixpanel_jql_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("mixpanel_jql_pipeline").dataset()
   # get events table as Pandas frame
   data.events.df().head()
    ```

## Running into errors?

Mixpanel API requires HTTPS for all calls; HTTP calls are rejected. It enforces strict rate limits with a maximum of 60 queries per hour and 5 concurrent queries. The API uses gzip content encoding and supports JSON and x-ndjson content types. There are stringent requirements for event data formatting, including limits on property numbers and nesting depth. Queries have a timeout after 2 minutes, and there's a rate limit of 2GB of uncompressed JSON per minute.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)