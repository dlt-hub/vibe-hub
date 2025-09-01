In this guide, we'll set up a complete Buffer data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def buffer_profiles_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.bufferapp.com/1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "profiles",
            "updates",
            "user"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='buffer_profiles_pipeline',
        destination='duckdb',
        dataset_name='buffer_profiles_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(buffer_profiles_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from buffer_profiles’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Profiles: Access and manage user profiles.
- Updates: Manage updates for profiles, including creating, editing, and deleting.
- Schedules: Manage posting schedules for profiles.

You can combine these endpoints to build pipelines that extract structured content from Buffer workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@buffer_profiles-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Buffer support.
    ```shell
    dlt init dlthub:buffer_profiles duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@buffer_profiles-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Buffer API, as specified in @buffer_profiles-docs.yaml 
    Start with endpoints "profiles" and "updates" and skip incremental loading for now. 
    Place the code in buffer_profiles_pipeline.py and name the pipeline buffer_profiles_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python buffer_profiles_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Buffer uses OAuth 2.0 for authentication. The authorization process involves obtaining an access token from the token URL using a client ID and secret, which must be included in the Authorization header of subsequent requests.
    
    To get appropriate API keys, please visit the original source at https://buffer.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python buffer_profiles_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline buffer_profiles load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset buffer_profiles_data
    The duckdb destination used duckdb:/buffer_profiles.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline buffer_profiles_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("buffer_profiles_pipeline").dataset()
   # get "profiles" table as Pandas frame
   data."profiles".df().head()
    ```

## Running into errors?

The Buffer API is rate limited to 60 authenticated requests per user per minute. Exceeding this limit results in a HTTP 429 error. All requests should be in JSON format. There are specific restrictions and errors related to parameters, authorization, and request limits that should be carefully managed to avoid common pitfalls such as duplicate content, posting limits, or unsupported media types.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)