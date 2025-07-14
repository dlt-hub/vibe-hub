In this guide, we'll set up a complete Shortcut data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def shortcut_project_management_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.shortcut.com/api/v3/",
            "auth": {
                "type": "apikey",
                "api_key": {{ dlt.secrets['api_key'] }},
    "location": "header",
    "header_name": "Shortcut-Token"
},
        },
        "resources": [
            "epics",
            "categories",
            "files"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='shortcut_project_management_pipeline',
        destination='duckdb',
        dataset_name='shortcut_project_management_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(shortcut_project_management_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from shortcut_project_management’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Epics: Operations related to epics, including searching, listing, and managing comments and stories within epics.
- Categories: Manage categories, milestones, and objectives associated with categories.
- Custom Fields: Retrieve information about custom fields defined within the system.
- Workflow: Operations related to epic workflows, including retrieving workflow details.
- Files & Groups: Manage files and group-related stories.

You can combine these endpoints to build pipelines that extract structured content from Shortcut workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@shortcut_project_management-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Shortcut support.
    ```shell
    dlt init dlthub:shortcut_project_management duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Shortcut API, as specified in @shortcut_project_management-docs.yaml 
    Start with endpoints "epics" and "categories" and skip incremental loading for now. 
    Place the code in shortcut_project_management_pipeline.py and name the pipeline shortcut_project_management_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python shortcut_project_management_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication requires an API key provided in the header of each request under the name 'Shortcut-Token'.
    
    To get appropriate API keys, please visit the original source at https://shortcut.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python shortcut_project_management_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline shortcut_project_management load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset shortcut_project_management_data
    The duckdb destination used duckdb:/shortcut_project_management.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline shortcut_project_management_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("shortcut_project_management_pipeline").dataset()
   # get epics table as Pandas frame
   data.epics.df().head()
    ```

## Running into errors?

Important considerations include managing rate limits on API calls, ensuring API key validity to avoid 401 Unauthorized errors, and correctly specifying endpoint paths and parameters to prevent 404 Not Found errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)