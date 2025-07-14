In this guide, we'll set up a complete GitLab data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def gitlab_migration_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://gitlab.example.com/api/v4/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "pipelines",
            "feature_flags",
            "pages_deployments"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='gitlab_migration_pipeline',
        destination='duckdb',
        dataset_name='gitlab_migration_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(gitlab_migration_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from gitlab_migration’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Pipeline Management: Manage CI/CD pipelines, including creation, retrieval, and management of pipeline configurations.
- Feature Flags: Handle the feature flags across the application, enabling or disabling features dynamically.
- GitLab Pages: Configure and manage GitLab Pages, including deployments and settings adjustments.
- Environments and Deployments: Work with project environments and manage deployments within those environments, including detailed configurations and status updates.

You can combine these endpoints to build pipelines that extract structured content from GitLab workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@gitlab_migration-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with GitLab support.
    ```shell
    dlt init dlthub:gitlab_migration duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@gitlab_migration-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for GitLab API, as specified in @gitlab_migration-docs.yaml 
    Start with endpoints "pipelines" and "feature_flags" and skip incremental loading for now. 
    Place the code in gitlab_migration_pipeline.py and name the pipeline gitlab_migration_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python gitlab_migration_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is performed using a 'PRIVATE-TOKEN' provided in the header of HTTP requests. This token acts as a bearer token to authenticate API requests.
    
    To get appropriate API keys, please visit the original source at https://about.gitlab.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python gitlab_migration_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline gitlab_migration load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset gitlab_migration_data
    The duckdb destination used duckdb:/gitlab_migration.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline gitlab_migration_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("gitlab_migration_pipeline").dataset()
   # get "pipelines" table as Pandas frame
   data."pipelines".df().head()
    ```

## Running into errors?

Be aware of the specific configuration requirements for GitLab Pages, such as the necessity to configure the daemon before use and the limitations on URL schemes. Additionally, the handling of feature flags and their lifecycle in production environments should be carefully managed to avoid exposing incomplete features. Furthermore, the deployment environments have specific constraints, such as the inability to rename or set the tier through the UI.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)