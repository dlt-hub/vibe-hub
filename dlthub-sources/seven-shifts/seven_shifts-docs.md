In this guide, we'll set up a complete 7shifts data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def seven_shifts_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.7shifts.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/companies",
            "/locations",
            "/departments"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='seven_shifts_pipeline',
        destination='duckdb',
        dataset_name='seven_shifts_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(seven_shifts_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from seven_shifts’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Companies: Retrieve information about companies.
- Locations: Fetch data related to different locations.
- Departments: Access details about departments.
- Roles: Get data on various roles within the company.
- Users: Retrieve user information.
- Wages: Access wage information for employees.
- Assignments: Fetch assignment details.
- Location Assignments: Get assignment details specific to locations.
- Department Assignments: Access assignments specific to departments.
- Role Assignments: Retrieve role-specific assignment details.
- Time Punches: Access records of time punches.
- Shifts: Get data on shifts.

You can combine these endpoints to build pipelines that extract structured content from 7shifts workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@seven_shifts-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

### 1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with 7shifts support.
    ```shell
    dlt init dlthub:seven_shifts duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
### 2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for 7shifts API, as specified in @seven_shifts-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in seven_shifts_pipeline.py and name the pipeline seven_shifts_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python seven_shifts_pipeline.py and await further instructions.
    ```

    
### 3. 🔒 **Setup credentials** 
    
    Authentication is required using an API key passed as a header. The header name is 'Authorization' and the key should be prefixed with 'Bearer '.
    
    To get appropriate API keys, please visit the original source at https://www.7shifts.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
### 4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python seven_shifts_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline seven_shifts load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset seven_shifts_data
    The duckdb destination used duckdb:/seven_shifts.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
### 5. 📈 **See data**
    
    ```shell
    dlt pipeline seven_shifts_pipeline show --marimo
    ```
    
### 6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("seven_shifts_pipeline").dataset()
   # get "/companies" table as Pandas frame
   data."/companies".df().head()
    ```

## Running into errors?

There is no pagination support for wages, assignments, location_assignments, department_assignments, and role_assignments. Users should handle potential large data returns efficiently. Always ensure the access token is valid to avoid 401 Unauthorized errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)