In this guide, we'll set up a complete TestRail data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def test_rail_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.testrail.com/api/v1/",
            "auth": {
                "type": "basic",
                "username": access_username,
                "password": access_password
            },
        },
        "resources": [
            "projects",
            "priorities",
            "plans"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='test_rail_pipeline',
        destination='duckdb',
        dataset_name='test_rail_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(test_rail_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from test_rail’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Projects: Retrieve information about projects.
- Priorities: Access priority levels defined within the system.
- Plans: Manage and retrieve test plans.
- Roles: Retrieve roles and associated permissions.
- Runs: Operations related to test runs.
- Result Fields: Retrieve fields available in test results.
- Suites: Manage test suites.
- Templates: Fetch available templates for cases and runs.
- Tests: Retrieve details about specific tests.
- Users: Operations related to user accounts.
- Sections: Manage sections within test suites.
- Case Statuses: Retrieve the various case statuses.
- Milestones: Manage project milestones.
- Datasets: Access datasets within the system.
- Configurations: Retrieve system configurations.
- Case Types: Get information about different case types.
- Case Fields: Manage and retrieve custom fields for test cases.
- Cases: Retrieve and manage test cases.

You can combine these endpoints to build pipelines that extract structured content from TestRail workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@test_rail-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with TestRail support.
    ```shell
    dlt init dlthub:test_rail duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for TestRail API, as specified in @test_rail-docs.yaml 
    Start with endpoints "projects" and "priorities" and skip incremental loading for now. 
    Place the code in test_rail_pipeline.py and name the pipeline test_rail_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python test_rail_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is performed using Basic HTTP authentication. It requires a valid username and password.
    
    To get appropriate API keys, please visit the original source at https://www.gurock.com/testrail/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python test_rail_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline test_rail load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset test_rail_data
    The duckdb destination used duckdb:/test_rail.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline test_rail_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("test_rail_pipeline").dataset()
   # get projects table as Pandas frame
   data.projects.df().head()
    ```

## Running into errors?

Ensure the username and password used for authentication are correct and that the user has appropriate permissions to access the desired resources. Handle 401 Unauthorized errors by verifying credentials and 403 Forbidden errors by checking user permissions.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)