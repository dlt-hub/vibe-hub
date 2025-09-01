In this guide, we'll set up a complete BART data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def bart_transport_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "http://api.bart.gov/api/",
            "auth": {
                "type": "apikey",
                "token": access_token,
            },
        },
        "resources": [
            "arrive",
            "depart",
            "advisories"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='bart_transport_pipeline',
        destination='duckdb',
        dataset_name='bart_transport_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(bart_transport_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from bart_transport’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Scheduling: Provides access to arrival and departure times, along with specific scheduling details.
- Station Information: Offers detailed data on stations including accessibility.
- Advisory Information: Gives updates on advisories that may affect service.
- Real-Time Estimates: Access to real-time estimates for train arrivals.
- Route Information: Details on routes including stops and service updates.
- Fare Information: Information on fare costs, including special fare classes and deprecations.

You can combine these endpoints to build pipelines that extract structured content from BART workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@bart_transport-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with BART support.
    ```shell
    dlt init dlthub:bart_transport duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@bart_transport-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for BART API, as specified in @bart_transport-docs.yaml 
    Start with endpoints "arrive" and "depart" and skip incremental loading for now. 
    Place the code in bart_transport_pipeline.py and name the pipeline bart_transport_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python bart_transport_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication for the BART API utilizes an API key that must be included in each request.
    
    To get appropriate API keys, please visit the original source at https://www.bart.gov/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python bart_transport_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline bart_transport load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset bart_transport_data
    The duckdb destination used duckdb:/bart_transport.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline bart_transport_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("bart_transport_pipeline").dataset()
   # get "arrive" table as Pandas frame
   data."arrive".df().head()
    ```

## Running into errors?

Updated API version recently from 1.01 to 2.0, which may affect existing integrations. XML is the default output format; JSON output must be explicitly requested. Be aware of deprecated attributes in the fare information and ensure utilizing the latest attributes like 'fares' instead of 'fare' and 'clipper'. Special schedule messages are displayed only under specific conditions. Error messages and adjustments to route information or scheduling might occur with new releases.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)