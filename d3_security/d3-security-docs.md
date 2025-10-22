In this guide, we'll set up a complete D3 Security data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def d3_security_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.github.com/users/d3sysint",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "followers", "subscriptions"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='d3_security_pipeline',
        destination='duckdb',
        dataset_name='d3_security_data', 
    )
    # Load the data
    load_info = pipeline.run(d3_security_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from d3_security’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **User Events**: Endpoints related to events associated with a specific user, including received events and public/private events.
  - `/users/d3sysint/received_events`: Events that the user has received.
  - `/users/d3sysint/events{/privacy`: Events related to the user, with optional privacy setting.
  
- **User Profile**: Endpoints that provide information about the user's profile and activities.
  - `/users/d3sysint`: Basic user profile information.
  - `/users/d3sysint/repos`: Repositories owned by the user.
  - `/users/d3sysint/orgs`: Organizations the user is a member of.
  
- **User Following**: Endpoints related to following activities of the user.
  - `/users/d3sysint/following{/other_user`: Users that the specified user is following.
  - `/users/d3sysint/followers`: Users that follow the specified user.

- **User Gists**: Endpoints for managing gists created by the user.
  - `/users/d3sysint/gists{/gist_id`: Gists created by the user, with optional gist ID.

- **User Starred Repositories**: Endpoints for repositories that the user has starred.
  - `/users/d3sysint/starred{/owner`: Starred repositories by the user, with optional owner parameter.

- **User Subscriptions**: Information about repositories that the user is subscribed to.
  - `/users/d3sysint/subscriptions`: Subscriptions of the user to repositories.

You will then debug the D3 Security pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - We suggest using a model like Claude 3.7 Sonnet or better
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started!

1. ⚙️ **Set up `dlt` Workspace**
    
    Install dlt with duckdb support:
    ```shell
    pip install "dlt[workspace]"
    ```

    Initialize a dlt pipeline with D3 Security support.
    ```shell
    dlt init dlthub:d3_security duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for D3 Security API, as specified in @d3_security-docs.yaml 
    Start with endpoints followers and subscriptions and skip incremental loading for now. 
    Place the code in d3_security_pipeline.py and name the pipeline d3_security_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python d3_security_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate, you can use Basic Authentication by inputting your User Name and password, or Bearer Authentication by inputting your Bearer Token, for which more information can be found in the OAuth 2.0 Bearer Token Usage documentation.
    
    To get the appropriate API keys, please visit the original source at https://docs.d3security.com/integration-docs/integration-docs/rest-api-callback.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python d3_security_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline d3_security load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset d3_security_data
    The duckdb destination used duckdb:/d3_security.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline d3_security_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("d3_security_pipeline").dataset()
   # get "followers" table as Pandas frame
   data."followers".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
