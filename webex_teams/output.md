In this guide, we'll set up a complete Webex data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def webex_communications_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://webexapis.com/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "authorize",
            "access_token",
            "userinfo"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='webex_communications_pipeline',
        destination='duckdb',
        dataset_name='webex_communications_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(webex_communications_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from webex_communications’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Authorization: Handles OAuth2 authorization and token management.
- User Information: Retrieves information about the user associated with the access token.
- Meeting Management: Manages and schedules Webex meetings.
- Status Information: Provides status updates and maintenance information about Webex services.

You can combine these endpoints to build pipelines that extract structured content from Webex workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@webex_communications-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Webex support.
    ```shell
    dlt init dlthub:webex_communications duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@webex_communications-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Webex API, as specified in @webex_communications-docs.yaml 
    Start with endpoints "authorize" and "access_token" and skip incremental loading for now. 
    Place the code in webex_communications_pipeline.py and name the pipeline webex_communications_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python webex_communications_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Webex uses OAuth2 for authentication. Tokens are passed via the Authorization header using the Bearer scheme. The API supports both refresh tokens and access tokens, with access tokens having a validity of 14 days and refresh tokens expiring in 90 days.
    
    To get appropriate API keys, please visit the original source at https://www.webex.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python webex_communications_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline webex_communications load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset webex_communications_data
    The duckdb destination used duckdb:/webex_communications.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline webex_communications_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("webex_communications_pipeline").dataset()
   # get "authorize" table as Pandas frame
   data."authorize".df().head()
    ```

## Running into errors?

Webex API usage includes rate limits of 300 requests per minute for most endpoints. Access tokens are valid for only 14 days, requiring regular refresh. The API environment is limited to 10 users for Sandbox testing, and certain features like the Meetings widget are not supported on mobile browsers. Compliance with OAuth scopes is strictly enforced.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)