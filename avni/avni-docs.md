In this guide, we'll set up a complete Avni data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def avni_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://app.avniproject.org/api/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "user-groups",
            "web/media",
            "api/subjects"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='avni_pipeline',
        destination='duckdb',
        dataset_name='avni_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(avni_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from avni’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- User Groups: Manage and retrieve user groups.
- Media Access: Access media via URLs.
- Aggregate Queries: Perform aggregate queries for reporting.
- Subject Management: Manage and retrieve subject information.
- Enrolments: Manage and retrieve enrolment details.
- Encounter Management: Manage and retrieve encounter details.

You can combine these endpoints to build pipelines that extract structured content from Avni workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@avni-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Avni support.
    ```shell
    dlt init dlthub:avni duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Avni API, as specified in @avni-docs.yaml 
    Start with endpoints "user-groups" and "web/media" and skip incremental loading for now. 
    Place the code in avni_pipeline.py and name the pipeline avni_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python avni_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Avni uses AWS Cognito for authentication. The authentication token is sent as a header, and refresh tokens are used for maintaining session validity.
    
    To get appropriate API keys, please visit the original source at https://avniproject.org/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python avni_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline avni load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset avni_data
    The duckdb destination used duckdb:/avni.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline avni_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("avni_pipeline").dataset()
   # get user-groups table as Pandas frame
   data.user-groups.df().head()
    ```

## Running into errors?

Access control is not currently applied on endpoints that save data from the mobile app, which is planned for future implementation. Difficulties in performing access checks on search requests mean that basic details for entities matching search criteria are visible to all users. Integration service does not use OAuth or other third-party identity providers like Cognito or Keycloak yet. All configurations for the integration service are managed via system environment variables.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)