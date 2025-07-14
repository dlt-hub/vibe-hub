In this guide, we'll set up a complete YouTube Reporting data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def youtube_reporting_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://youtube.googleapis.com/youtube/reporting/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
,
        },
        "resources": [
            "channel_annotations_a1",
            "channel_basic_a2",
            "channel_cards_a1"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='youtube_reporting_pipeline',
        destination='duckdb',
        dataset_name='youtube_reporting_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(youtube_reporting_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from youtube_reporting’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Channel Reports: Reports related to channel analytics like annotations, demographics, device OS, etc.
- Playlist Reports: Reports focusing on playlist analytics including device OS, playback locations, traffic sources, etc.

You can combine these endpoints to build pipelines that extract structured content from YouTube Reporting workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@youtube_reporting-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with YouTube Reporting support.
    ```shell
    dlt init dlthub:youtube_reporting duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for YouTube Reporting API, as specified in @youtube_reporting-docs.yaml 
    Start with endpoints "channel_annotations_a1" and "channel_basic_a2" and skip incremental loading for now. 
    Place the code in youtube_reporting_pipeline.py and name the pipeline youtube_reporting_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python youtube_reporting_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is handled via OAuth 2.0, requiring a refresh token flow. Access tokens are obtained through the token URL using client ID, client secret, and refresh token. The Authorization header is used to include the obtained access token in API requests.
    
    To get appropriate API keys, please visit the original source at https://www.youtube.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python youtube_reporting_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline youtube_reporting load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset youtube_reporting_data
    The duckdb destination used duckdb:/youtube_reporting.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline youtube_reporting_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("youtube_reporting_pipeline").dataset()
   # get channel_annotations_a1 table as Pandas frame
   data.channel_annotations_a1.df().head()
    ```

## Running into errors?

Reporting jobs need to be created for reports to be generated. Historical data is available only for the 30-day period prior to job creation. Be aware of API request limits to avoid throttling and ensure proper handling of 401 Unauthorized errors by rechecking OAuth scopes or token expiration.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)