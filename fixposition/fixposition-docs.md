In this guide, we'll set up a complete Fixposition data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def fixposition_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "http://10.0.2.1/api/v2/params/config",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "def", "reset"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='fixposition_pipeline',
        destination='duckdb',
        dataset_name='fixposition_data', 
    )
    # Load the data
    load_info = pipeline.run(fixposition_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from fixposition’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Fusion Control**: Endpoints related to controlling and managing fusion processes.
  - `/api/v2/fusion/ctrl`: Controls the fusion process.

- **Log Management**: Endpoints for downloading and managing log files.
  - `/api/v2/log/dl`: Downloads specific log files from internal storage.
  - `/api/v2/log/disk`: Provides information regarding log storage.

- **Camera Management**: Endpoints for camera calibration, recording, and management.
  - `/api/v2/camera/calib_upl`: Uploads camera calibration data.
  - `/api/v2/camera/rec_rm`: Removes a recorded video.
  - `/api/v2/camera/rec_ls`: Lists recorded videos.
  - `/api/v2/camera/record`: Starts recording video.
  - `/api/v2/camera/rec_dl`: Downloads a specific recorded video file.

- **Network Configuration**: Endpoints for managing network settings.
  - `/api/v2/net/wifi_add`: Adds a new Wi-Fi connection.
  - `/api/v2/net/wifi_remove`: Removes an existing Wi-Fi connection.
  - `/api/v2/net/conn_set`: Sets connection parameters.

- **Recording Control**: Endpoints for starting and managing recordings.
  - `/api/v2/record/info`: Provides information about current recordings.
  - `/api/v2/record/start`: Starts a new recording.

- **Backup Settings**: Endpoints for configuring backup options.
  - `/api/v2/backup/set`: Sets backup configurations.

- **Parameter Management**: Endpoints for getting and setting hardware and configuration parameters.
  - `/api/v2/params/hw/get`: Retrieves hardware parameters.
  - `/api/v2/params/config/set`: Sets configuration parameters.

- **System Status**: Endpoints for checking the status of the system and components.
  - `/api/v2/ctrl/status`: Retrieves the current status of the system.

- **Map Management**: Endpoint for managing map tokens.
  - `/api/v2/map/token_reset`: Resets map tokens.

You will then debug the Fixposition pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Fixposition support.
    ```shell
    dlt init dlthub:fixposition duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Fixposition API, as specified in @fixposition-docs.yaml 
    Start with endpoints def and reset and skip incremental loading for now. 
    Place the code in fixposition_pipeline.py and name the pipeline fixposition_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python fixposition_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    A token is required, which can be obtained by making a POST request to **/api/v2/map/token_get** with the body `{"which": "mapbox"}`, and it should be applied in the POST request to **/api/v2/map/token_set** as `{"which": "mapbox", "token": "verysecretnot"}`.
    
    To get the appropriate API keys, please visit the original source at https://docs.fixposition.com/fd/api-documentation.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python fixposition_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline fixposition load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset fixposition_data
    The duckdb destination used duckdb:/fixposition.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline fixposition_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("fixposition_pipeline").dataset()
   # get "def" table as Pandas frame
   data."def".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
