In this guide, we'll set up a complete Jira data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def jira_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-domain.atlassian.net/rest/api/3/",
            "auth": {
                "type": "basic",
                "username": "your_email@example.com",
                "password": "<JIRA_API_KEY>"
            },
        },
        "resources": [
            "applicationrole",
            "avatar/type/system",
            "board"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='jira_pipeline',
        destination='duckdb',
        dataset_name='jira_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(jira_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from jira’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Application Roles: Provides information about the different application roles available within Jira.
- Avatars: Manages system and project avatars.
- Boards: Retrieves boards and their related data for agile projects.
- Dashboards: Access and manage dashboard configurations.
- Filters and Filter Sharing: Manage search filters and their sharing permissions.
- Groups: Query and manage user groups.
- Issue Fields and Configurations: Handle definitions and configurations of issue fields, including custom fields.

You can combine these endpoints to build pipelines that extract structured content from Jira workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@jira-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Jira support.
    ```shell
    dlt init dlthub:jira duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Jira API, as specified in @jira-docs.yaml 
    Start with endpoints "applicationrole" and "avatar/type/system" and skip incremental loading for now. 
    Place the code in jira_pipeline.py and name the pipeline jira_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python jira_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is primarily done through API tokens with Basic Auth method. The API token must be included in the request header.
    
    To get appropriate API keys, please visit the original source at https://www.atlassian.com/software/jira.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python jira_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline jira load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset jira_data
    The duckdb destination used duckdb:/jira.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline jira_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("jira_pipeline").dataset()
   # get applicationrole table as Pandas frame
   data.applicationrole.df().head()
    ```

## Running into errors?

Users need to ensure that they have the necessary permissions to access specific endpoints. Rate limits may apply, potentially affecting the frequency and volume of API calls.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)