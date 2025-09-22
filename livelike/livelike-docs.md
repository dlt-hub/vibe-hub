In this guide, we'll set up a complete Livelike data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def livelike_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://cf-blast.livelikecdn.com/api/v1/applications/%7Bclient_id%7D",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "prizeout-callback-balance", "prizeout-callback-session"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='livelike_pipeline',
        destination='duckdb',
        dataset_name='livelike_data', 
    )
    # Load the data
    load_info = pipeline.run(livelike_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from livelike’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Prizeout Callbacks**: Endpoints for handling various callback events related to the Prizeout feature, including balance checks, session management, success, and failure notifications.
  - `/api/v1/applications/{client_id}/prizeout-callback-balance`: To check the balance for the Prizeout feature.
  - `/api/v1/applications/{client_id}/prizeout-callback-session`: To manage Prizeout session callbacks.
  - `/api/v1/applications/{client_id}/prizeout-callback-failure`: To handle failures during the Prizeout process.
  - `/api/v1/applications/{client_id}/prizeout-callback-success`: To process successful Prizeout transactions.

- **Chat Rooms**: Endpoint for managing chat rooms by their unique identifier.
  - `/api/v1/chat-rooms/:id`: To access or interact with a specific chat room based on its ID.

You will then debug the Livelike pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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
    pip install dlt[workspace]
    ```

    Initialize a dlt pipeline with Livelike support.
    ```shell
    dlt init dlthub:livelike duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Livelike API, as specified in @livelike-docs.yaml 
    Start with endpoints prizeout-callback-balance and prizeout-callback-session and skip incremental loading for now. 
    Place the code in livelike_pipeline.py and name the pipeline livelike_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python livelike_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    You will need your Client ID to initialize the Engagement SDK, which can be retrieved by following the instructions in [Retrieving your Client ID](/docs/retrieving-important-keys#section-retrieving-client-id), and you should use the Client ID to create the EngagementSDK instance with the code `final sdk = EngagementSDK("<client-id>");`.
    
    To get the appropriate API keys, please visit the original source at https://docs.livelike.com/docs/react-native-api-reference.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python livelike_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline livelike load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset livelike_data
    The duckdb destination used duckdb:/livelike.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline livelike_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("livelike_pipeline").dataset()
   # get "prizeout-callback-balance" table as Pandas frame
   data."prizeout-callback-balance".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)