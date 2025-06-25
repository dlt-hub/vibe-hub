In this guide, we'll set up a complete Incident.io data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def incident_io_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.incident.io/",
            "auth": {
                "type": "apikey",
                "api_key": access_token,
            },
        },
        "resources": [
            "actions",
            "catalog_types",
            "custom_fields"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='incident_io_pipeline',
        destination='duckdb',
        dataset_name='incident_io_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(incident_io_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from incident_io’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Actions: Retrieve various actions related to incidents.
- Catalog Types: Access different catalog types which classify incidents.
- Custom Fields: Manage custom fields to tailor incident data.
- Follow-Ups: Handle follow-up tasks post-incident resolution.
- Incident Roles: Define and retrieve roles assigned during incidents.
- Incident Timestamps: Track time-specific data for incidents.
- Incident Updates: Get updates made to ongoing or past incidents.
- Incident Statuses: View the different statuses an incident can have.
- Workflows: Configure and retrieve workflows for incident management.
- Users: Access user data related to incidents.
- Severities: Manage severity levels for incidents.
- Schedules: Manage schedules for incident handling.
- Incidents: Directly manage or query incidents.

You can combine these endpoints to build pipelines that extract structured content from Incident.io workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@incident_io-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Incident.io support.
    ```shell
    dlt init dlthub:incident_io duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Incident.io API, as specified in @incident_io-docs.yaml 
    Start with endpoints "actions" and "catalog_types" and skip incremental loading for now. 
    Place the code in incident_io_pipeline.py and name the pipeline incident_io_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python incident_io_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done using an API key. The API key needs to be passed in the headers for each request to authenticate and gain access.
    
    To get appropriate API keys, please visit the original source at https://www.incident.io/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python incident_io_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline incident_io load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset incident_io_data
    The duckdb destination used duckdb:/incident_io.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline incident_io_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("incident_io_pipeline").dataset()
   # get actions table as Pandas frame
   data.actions.df().head()
    ```

## Running into errors?

Ensure that the API key is valid and has the necessary permissions to perform requested operations. Handling errors such as '401 Unauthorized' will be crucial for debugging and proper API integration.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)