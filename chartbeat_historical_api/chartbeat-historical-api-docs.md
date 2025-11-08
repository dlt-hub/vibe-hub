In this guide, we'll set up a complete Chartbeat data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def chartbeat_historical_api_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.chartbeat.com/query/v2",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "recurring", "submit"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='chartbeat_historical_api_pipeline',
        destination='duckdb',
        dataset_name='chartbeat_historical_api_data', 
    )
    # Load the data
    load_info = pipeline.run(chartbeat_historical_api_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from chartbeat_historical_api’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Status Endpoints**: These endpoints provide the current status of queries or recurring tasks.
  - `/query/v2/status/`: Checks the status of a specific query.
  - `/query/v2/recurring/status/`: Checks the status of recurring queries.
  
- **Fetch Endpoints**: Used to retrieve data from queries or recurring tasks.
  - `/query/v2/fetch/`: Fetches the results of a submitted query.
  - `/query/v2/recurring/fetch/`: Fetches the results of a recurring query.

- **Submit Endpoints**: For submitting new queries or recurring tasks.
  - `/query/v2/submit/`: Submits a new query for execution.
  - `/query/v2/recurring/submit/`: Submits a new recurring query.

- **Runs and Cancel Endpoints**: Manage recurring tasks.
  - `/query/v2/recurring/runs/`: Retrieves the run history of recurring queries.
  - `/query/v2/recurring/cancel/`: Cancels a recurring query. 

- **Submit with Parameters**: Submits a query with specific parameters for filtering and sorting.
  - `/query/v2/submit/page/?host=YOUR_HOST&limit=100&start=YYYY-MM-DD&end=YYYY-MM-DD&sort_column=page_views&sort_order=desc&tz=America/New_York&metrics=page_views,page_uniques&dimensions=path,title`: Submits a page query with detailed parameters.

You will then debug the Chartbeat pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Chartbeat support.
    ```shell
    dlt init dlthub:chartbeat_historical_api duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Chartbeat API, as specified in @chartbeat_historical_api-docs.yaml 
    Start with endpoints recurring and submit and skip incremental loading for now. 
    Place the code in chartbeat_historical_api_pipeline.py and name the pipeline chartbeat_historical_api_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python chartbeat_historical_api_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    You need an API key, which should be passed in the HTTP header instead of as a query parameter.
    
    To get the appropriate API keys, please visit the original source at https://docs.chartbeat.com/cbp/api/historical-api/one-time-queries.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python chartbeat_historical_api_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline chartbeat_historical_api load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset chartbeat_historical_api_data
    The duckdb destination used duckdb:/chartbeat_historical_api.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline chartbeat_historical_api_pipeline show
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("chartbeat_historical_api_pipeline").dataset()
   # get "recurring" table as Pandas frame
   data."recurring".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
