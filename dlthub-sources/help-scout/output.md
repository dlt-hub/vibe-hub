In this guide, we'll set up a complete Help Scout Seed data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def help_scout_seed_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.helpscout.net/v2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "base",
            "reset",
            "alert"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='help_scout_seed_pipeline',
        destination='duckdb',
        dataset_name='help_scout_seed_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(help_scout_seed_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from help_scout_seed’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Base Resources: Include basic operations like getting base settings.
- UI Components: Access to user interface components such as buttons, cards, modals, etc.
- Form Elements: Manage form and input elements like dropdowns, input fields, and form groups.

You can combine these endpoints to build pipelines that extract structured content from Help Scout Seed workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@help_scout_seed-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Help Scout Seed support.
    ```shell
    dlt init dlthub:help_scout_seed duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@help_scout_seed-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Help Scout Seed API, as specified in @help_scout_seed-docs.yaml 
    Start with endpoints "base" and "reset" and skip incremental loading for now. 
    Place the code in help_scout_seed_pipeline.py and name the pipeline help_scout_seed_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python help_scout_seed_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Help Scout Seed uses OAuth 2.0 for authentication. It includes an authorization code flow where the client must acquire an access token using a client ID and secret. The token is then used in the Authorization header as a Bearer token for subsequent API requests.
    
    To get appropriate API keys, please visit the original source at https://www.helpscout.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python help_scout_seed_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline help_scout_seed load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset help_scout_seed_data
    The duckdb destination used duckdb:/help_scout_seed.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline help_scout_seed_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("help_scout_seed_pipeline").dataset()
   # get "base" table as Pandas frame
   data."base".df().head()
    ```

## Running into errors?

The API has strict rate limits with a cap of 200 requests per minute and additional limits for write operations. It's also important to handle possible errors such as '429: Too Many Requests' and '423 Locked' when updating resources with extensive data.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)