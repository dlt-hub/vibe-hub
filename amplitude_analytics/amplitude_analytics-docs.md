In this guide, we'll set up a complete Amplitude data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def amplitude_analytics_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.amplitude.com/",
            "auth": {
                "type": "apikey",
                "client_id": access_key,
                "client_secret": secret_key,
                "location": "query"
            },
        },
        "resources": [
            "active-users",
            "annotations",
            "average-session-length"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='amplitude_analytics_pipeline',
        destination='duckdb',
        dataset_name='amplitude_analytics_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(amplitude_analytics_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from amplitude_analytics’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- User Behavior Analysis: Provides insights into active user counts, average session length, etc.
- Event Tracking: Tracks and exports event data for detailed analysis.
- Cohort Analysis: Allows the segmentation of users into cohorts for behavioral analysis.
- Annotations: Supports the creation and retrieval of annotations to mark significant events within data streams.

You can combine these endpoints to build pipelines that extract structured content from Amplitude workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@amplitude_analytics-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Amplitude support.
    ```shell
    dlt init dlthub:amplitude_analytics duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Amplitude API, as specified in @amplitude_analytics-docs.yaml 
    Start with endpoints "active-users" and "annotations" and skip incremental loading for now. 
    Place the code in amplitude_analytics_pipeline.py and name the pipeline amplitude_analytics_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python amplitude_analytics_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication requires an API Key and a Secret Key passed as query parameters.
    
    To get appropriate API keys, please visit the original source at https://amplitude.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python amplitude_analytics_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline amplitude_analytics load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset amplitude_analytics_data
    The duckdb destination used duckdb:/amplitude_analytics.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline amplitude_analytics_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("amplitude_analytics_pipeline").dataset()
   # get active-users table as Pandas frame
   data.active-users.df().head()
    ```

## Running into errors?

Authentication must be carefully managed with both API Key and Secret Key. Incremental sync is supported only for specific endpoints such as active_users_counts and average_session_length, which means full data pulls might be required for others. Be aware of rate limits, as exceeding them can result in 429 Too Many Requests errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)