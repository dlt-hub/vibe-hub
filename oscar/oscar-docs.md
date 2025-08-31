In this guide, we'll set up a complete OSCAR data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def oscar_api_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.api-name.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "dashboard_endpoint",
            "service",
            "cluster"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='oscar_api_pipeline',
        destination='duckdb',
        dataset_name='oscar_api_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(oscar_api_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from oscar_api’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Dashboard Management: Manage and view various OSCAR dashboards such as Kubernetes, MinIO, and OSCAR itself. 
- Service Management: Deploy and manage services within the OSCAR environment including setting up web-based UIs and defining memory and CPU resources. 
- Storage Management: Handle data storage with providers such as MinIO and webdav, including input and output management for OSCAR services. 
- Authentication and Authorization: Manage OAuth2 flows and token refresh mechanisms, essential for secure interactions within the OSCAR ecosystem.

You can combine these endpoints to build pipelines that extract structured content from OSCAR workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@oscar_api-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with OSCAR support.
    ```shell
    dlt init dlthub:oscar_api duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@oscar_api-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for OSCAR API, as specified in @oscar_api-docs.yaml 
    Start with endpoints "dashboard_endpoint" and "service" and skip incremental loading for now. 
    Place the code in oscar_api_pipeline.py and name the pipeline oscar_api_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python oscar_api_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    OSCAR API uses OAuth2 for authentication. The tokens are used in the Authorization header to interact with the API securely. The OAuth2 flow includes obtaining access and refresh tokens via a token URL.
    
    To get appropriate API keys, please visit the original source at https://oscar.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python oscar_api_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline oscar_api load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset oscar_api_data
    The duckdb destination used duckdb:/oscar_api.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline oscar_api_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("oscar_api_pipeline").dataset()
   # get "dashboard_endpoint" table as Pandas frame
   data."dashboard_endpoint".df().head()
    ```

## Running into errors?

OSCAR API does not support DELETE or UPDATE operations and depends on OAuth2 with refresh token flow which requires proper configuration of connected apps. MinIO is required for storage and function triggering in OSCAR, and only MinIO can be used as an input storage provider. The API has specific requirements for the deployment environment, including DNS resolutions and service exposure configurations.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)