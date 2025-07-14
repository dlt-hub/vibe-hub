In this guide, we'll set up a complete Keeper Security data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def keeper_security_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://www.keepersecurity.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "vault",
            "desktop_electron",
            "mobile"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='keeper_security_pipeline',
        destination='duckdb',
        dataset_name='keeper_security_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(keeper_security_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from keeper_security’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Applications & Extensions: Endpoints related to different Keeper applications and extensions such as Web Vault, Desktop Application, Mobile App, and Browser Extension.
- Administration: Endpoints for user, roles, teams, and node management within the admin console.
- File Management: Endpoints for uploading attachments and managing file sharing.
- Record Management: Endpoints for managing default, custom fields, and custom record types.

You can combine these endpoints to build pipelines that extract structured content from Keeper Security workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@keeper_security-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Keeper Security support.
    ```shell
    dlt init dlthub:keeper_security duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@keeper_security-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Keeper Security API, as specified in @keeper_security-docs.yaml 
    Start with endpoints "vault" and "desktop_electron" and skip incremental loading for now. 
    Place the code in keeper_security_pipeline.py and name the pipeline keeper_security_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python keeper_security_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via OAuth2 with the token location in the header under 'Authorization'. A refresh token flow is used for session continuity.
    
    To get appropriate API keys, please visit the original source at https://www.keepersecurity.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python keeper_security_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline keeper_security load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset keeper_security_data
    The duckdb destination used duckdb:/keeper_security.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline keeper_security_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("keeper_security_pipeline").dataset()
   # get "vault" table as Pandas frame
   data."vault".df().head()
    ```

## Running into errors?

Keeper limits the number of users to 5000 for performance reasons, and a compliance report is limited to 1000 records. The platform offers both on-premise and cloud versions, and it's important to ensure that only approved devices attempt login. Offline access requires re-authentication specifying the duration.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)