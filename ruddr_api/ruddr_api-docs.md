In this guide, we'll set up a complete Ruddr data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def ruddr_api_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.ruddr.com/",
            "auth": {
                "type": "apikey",
                "api_token": access_token,
                "location": "header",
                "header_name": "Authorization"
            },
        },
        "resources": [
            "clients",
            "projects",
            "project_members"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='ruddr_api_pipeline',
        destination='duckdb',
        dataset_name='ruddr_api_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(ruddr_api_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from ruddr_api’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Clients: Access details about clients.
- Projects: Manage and retrieve projects information.
- Project Members: Get information about members associated with projects.
- Project Tasks: Access tasks under specific projects.
- Expense Reports: Manage expense reporting.
- Expense Items: Details about individual expense items.
- Expense Categories: Categorization of expenses.
- Project Expense: Track expenses against projects.
- Project Budget Expenses: Manage and track expenses against project budgets.
- Workspace Members: Information about members of a workspace.
- Opportunity Stages: Stages of business opportunities.
- Invoices: Handling of invoicing.
- Holidays: Information about holidays.

You can combine these endpoints to build pipelines that extract structured content from Ruddr workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@ruddr_api-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Ruddr support.
    ```shell
    dlt init dlthub:ruddr_api duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Ruddr API, as specified in @ruddr_api-docs.yaml 
    Start with endpoints "clients" and "projects" and skip incremental loading for now. 
    Place the code in ruddr_api_pipeline.py and name the pipeline ruddr_api_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python ruddr_api_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done using an API key which must be included in the header of each request. The API key can be generated from the Ruddr workspace settings.
    
    To get appropriate API keys, please visit the original source at https://www.ruddr.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python ruddr_api_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline ruddr_api load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset ruddr_api_data
    The duckdb destination used duckdb:/ruddr_api.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline ruddr_api_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("ruddr_api_pipeline").dataset()
   # get clients table as Pandas frame
   data.clients.df().head()
    ```

## Running into errors?

Ensure that the API key is valid and correctly included in the header for each API request. Generate the API key from the Ruddr workspace settings under API Keys section.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)