In this guide, we'll set up a complete Anaplan data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def anaplan_functions_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.anaplan.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "functions/numeric",
            "functions/time_and_date",
            "functions/call_center_planning"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='anaplan_functions_pipeline',
        destination='duckdb',
        dataset_name='anaplan_functions_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(anaplan_functions_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from anaplan_functions’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Numeric Functions: Provides endpoints for numeric calculations.
- Time and Date Functions: Offers functionality related to time and date operations.
- Call Center Planning Functions: Specific functions for call center planning scenarios.
- Aggregation Functions: Endpoints for data aggregation methods.
- Financial Functions: Functions tailored for financial calculations.
- Logical Functions: Logical operation functions.
- Text Functions: Functions for text manipulation and processing.
- Miscellaneous Functions: A collection of various utility functions not covered in other categories.

You can combine these endpoints to build pipelines that extract structured content from Anaplan workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@anaplan_functions-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Anaplan support.
    ```shell
    dlt init dlthub:anaplan_functions duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@anaplan_functions-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Anaplan API, as specified in @anaplan_functions-docs.yaml 
    Start with endpoints "functions/numeric" and "functions/time_and_date" and skip incremental loading for now. 
    Place the code in anaplan_functions_pipeline.py and name the pipeline anaplan_functions_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python anaplan_functions_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The authentication method for Anaplan involves using OAuth2 with a refresh token mechanism. This requires setting up a connected app within Anaplan to obtain the client ID, client secret, and refresh token. Authentication is performed via headers, specifically with the 'Authorization' header.
    
    To get appropriate API keys, please visit the original source at https://www.anaplan.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python anaplan_functions_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline anaplan_functions load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset anaplan_functions_data
    The duckdb destination used duckdb:/anaplan_functions.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline anaplan_functions_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("anaplan_functions_pipeline").dataset()
   # get "functions/numeric" table as Pandas frame
   data."functions/numeric".df().head()
    ```

## Running into errors?

Users should be aware of the inactivity timeout which automatically logs out users after 35 minutes. Also, due to the nature of OAuth2 refresh token flow, maintaining current credentials and handling token refresh gracefully is crucial to prevent authentication errors. Additionally, managing data limits per worksheet and understanding the model-specific limitations are essential for effective use of Anaplan.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)