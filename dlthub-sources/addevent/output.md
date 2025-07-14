In this guide, we'll set up a complete AddEvent data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def add_event_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://cdn.addevent.com/libs/stc/1.0.2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "calendar",
            "event",
            "campaign_member"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='add_event_pipeline',
        destination='duckdb',
        dataset_name='add_event_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(add_event_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from add_event’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Calendar Management: Provides endpoints for managing calendars and events such as '/api/calendar', '/events', and '/calendars'.
- Event Handling: Includes endpoints for creating and handling event-specific actions like '/events', '/dir/'.
- Campaign and Contact Management: Contains endpoints to manage campaign members and contact details, exemplified by '/services/data/vXX.X/sobjects/CampaignMember' and '/services/data/vXX.X/sobjects/Contact'.

You can combine these endpoints to build pipelines that extract structured content from AddEvent workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@add_event-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with AddEvent support.
    ```shell
    dlt init dlthub:add_event duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@add_event-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for AddEvent API, as specified in @add_event-docs.yaml 
    Start with endpoints "calendar" and "event" and skip incremental loading for now. 
    Place the code in add_event_pipeline.py and name the pipeline add_event_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python add_event_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The authentication uses OAuth 2.0 with a refresh token flow. Important values such as client_id, client_secret, and refresh_token are stored securely and used to obtain access tokens from a specified token URL.
    
    To get appropriate API keys, please visit the original source at https://www.addevent.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python add_event_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline add_event load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset add_event_data
    The duckdb destination used duckdb:/add_event.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline add_event_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("add_event_pipeline").dataset()
   # get "calendar" table as Pandas frame
   data."calendar".df().head()
    ```

## Running into errors?

Ensure proper setup of the connected app for OAuth2 authentication. Be cautious with handling nulls in deeply nested fields of some objects like Contact. Understand the limitations and throttling behavior of API usage as outlined in the provided notes and errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)