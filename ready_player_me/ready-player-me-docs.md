In this guide, we'll set up a complete Ready Player Me data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def ready_player_me_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.readyplayer.me/v1/assets/:id",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "lock`", "unlock`"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='ready_player_me_pipeline',
        destination='duckdb',
        dataset_name='ready_player_me_data', 
    )
    # Load the data
    load_info = pipeline.run(ready_player_me_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from ready_player_me’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Avatars Endpoints**: Manage and manipulate avatars, including equipping and unequipping specific avatars.
  - `/v1/avatars/:id/equip`: Equip a specific avatar.
  - `/v1/avatars/:id/unequip`: Unequip a specific avatar.
  
- **Assets Endpoints**: Handle various operations related to assets, including retrieving, locking, unlocking, and managing asset models and icons.
  - `/v1/assets`: Retrieve a list of assets.
  - `/v1/assets/:id`: Get details of a specific asset.
  - `/v1/assets/:id/model`: Access the model of a specific asset.
  - `/v1/assets/:id/icon`: Access the icon of a specific asset.
  - `/v1/assets/:id/application`: Manage application-related details for a specific asset.
  - `/v1/assets/:id/lock`: Lock a specific asset.
  - `/v1/assets/:id/unlock`: Unlock a specific asset.
  
- **Temporary Media Endpoints**: Handle temporary media files.
  - `/v1/temporary-media`: Manage temporary media uploads and access.

You will then debug the Ready Player Me pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Ready Player Me support.
    ```shell
    dlt init dlthub:ready_player_me duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Ready Player Me API, as specified in @ready_player_me-docs.yaml 
    Start with endpoints lock` and unlock` and skip incremental loading for now. 
    Place the code in ready_player_me_pipeline.py and name the pipeline ready_player_me_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python ready_player_me_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To gain full access to all REST Avatar API endpoints, you need to create an API Key.
    
    To get the appropriate API keys, please visit the original source at https://docs.readyplayer.me/ready-player-me/customizing-guides/create-custom-assets/hairstyle.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python ready_player_me_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline ready_player_me load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset ready_player_me_data
    The duckdb destination used duckdb:/ready_player_me.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline ready_player_me_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("ready_player_me_pipeline").dataset()
   # get "lock`" table as Pandas frame
   data."lock`".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
