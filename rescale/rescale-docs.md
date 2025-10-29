In this guide, we'll set up a complete Rescale data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def rescale_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://platform.rescale.com/api/v2/jobs/XXXXX/runs/1",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "tail", "directory-contents"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='rescale_pipeline',
        destination='duckdb',
        dataset_name='rescale_data', 
    )
    # Load the data
    load_info = pipeline.run(rescale_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from rescale’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Job Endpoints**: Endpoints related to job management such as creating, retrieving, and sharing job details.
  - `/api/v2/jobs/aabbcc`: Retrieve details of a specific job.
  - `/api/v2/jobs/XXXXX/runs/1/tail/airFoil2D/0/U`: Get real-time output from a specific job run.
  - `/api/v2/jobs/XXXXX/runs/1/directory-contents`: Access directory contents of a specific job run.
  - `/api/v2/jobs/LeeKa/share`: Share a specific job.

- **Storage Device Endpoints**: Endpoints for managing and retrieving information regarding storage devices.
  - `/api/v2/storage-devices/BNTMk/statuses`: Get the status of a specific storage device.
  - `/api/v2/storage-devices/BNTMk/file-downloads`: Endpoint for downloading files from a specific storage device.
  - `/api/v2/storage-devices/available-regions/`: List available regions for storage devices.

- **Organization Endpoints**: Endpoints related to organizational information and management.
  - `/api/v2/organizations/{company_code`: Retrieve details about a specific organization.
  - `/api/v3/organizations/{organization`: Access details of a specific organization.

- **Workspace and Project Endpoints**: Endpoints for managing workspaces and projects within an organization.
  - `/api/v2/organizations/rescale/workspaces/00-046880176/projects/pCTMk`: Access a specific project within a workspace.
  - `/api/v2/organizations/rescale/workspaces/00-046880176/projects/pCTMk/groups/pkaaa`: Manage groups within a specific project.

- **User and Folder Endpoints**: Endpoints related to user data and folder management.
  - `/api/v3/users/me/folders`: Retrieve folders associated with the current user.
  - `/api/v3/folders/copy-to-device`: Copy a folder to a specified device.

- **Task and Budget Endpoints**: Endpoints for managing tasks and budgets.
  - `/api/v2/tasks/{token`: Manage specific tasks based on a token.
  - `/api/v3/budgets`: Access budget information.

You will then debug the Rescale pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Rescale support.
    ```shell
    dlt init dlthub:rescale duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Rescale API, as specified in @rescale-docs.yaml 
    Start with endpoints tail and directory-contents and skip incremental loading for now. 
    Place the code in rescale_pipeline.py and name the pipeline rescale_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python rescale_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate requests to the Rescale API, you will need an API token which can be generated [here](https://platform.rescale.com/user/settings/api-key/), and you must include it in the request header as `Authorization: Token <api-token>`, replacing `<api-token>` with your actual token.
    
    To get the appropriate API keys, please visit the original source at https://rescale.github.io/api-docs/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python rescale_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline rescale load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset rescale_data
    The duckdb destination used duckdb:/rescale.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline rescale_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("rescale_pipeline").dataset()
   # get "tail" table as Pandas frame
   data."tail".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
