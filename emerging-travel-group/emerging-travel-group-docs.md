In this guide, we'll set up a complete Emerging Travel Group data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def emerging_travel_group_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.worldota.net/api/b2b/v3/hotel/order/booking",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "finish", "form"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='emerging_travel_group_pipeline',
        destination='duckdb',
        dataset_name='emerging_travel_group_data', 
    )
    # Load the data
    load_info = pipeline.run(emerging_travel_group_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from emerging_travel_group’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Overview Endpoints**: Provides general information about the API and its capabilities.
  - `/api/b2b/v3/overview`: Gives an overview of the B2B API features.

- **Hotel Information Endpoints**: Retrieve details about specific hotels and related data.
  - `/api/b2b/v3/hotel/info/`: Gets detailed information about a specific hotel.
  - `/api/b2b/v3/hotel/info/?data={`: Similar to the previous endpoint but allows for additional data parameters.
  - `/api/b2b/v3/hotel/info/dump`: Provides a bulk dump of hotel information.
  - `/api/b2b/v3/hotel/info/incremental_dump`: Retrieves an incremental update of hotel information.

- **Hotel Search Endpoints**: Used for searching hotels based on various criteria.
  - `/api/b2b/v3/search/serp/hotels`: Searches for hotels and returns search results.
  - `/api/b2b/v3/search/serp/region`: Searches for hotels within a specific region.
  - `/api/b2b/v3/search/serp/geo`: Searches for hotels based on geographical coordinates.
  - `/api/b2b/v3/search/hp`: A high-performance search endpoint for hotels.

- **Hotel Booking Endpoints**: Manage the hotel booking process.
  - `/api/b2b/v3/hotel/order/booking/form`: Retrieves the booking form for a hotel.
  - `/api/b2b/v3/hotel/order/booking/finish`: Completes the booking process.
  - `/api/b2b/v3/hotel/order/booking/finish/status`: Checks the status of a completed booking.
  
- **Hotel Order Management Endpoints**: Handle hotel orders and cancellations.
  - `/api/b2b/v3/hotel/order/cancel`: Cancels an existing hotel order.
  - `/api/b2b/v3/hotel/order/info`: Retrieves information about a specific hotel order.

You will then debug the Emerging Travel Group pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Emerging Travel Group support.
    ```shell
    dlt init dlthub:emerging_travel_group duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Emerging Travel Group API, as specified in @emerging_travel_group-docs.yaml 
    Start with endpoints finish and form and skip incremental loading for now. 
    Place the code in emerging_travel_group_pipeline.py and name the pipeline emerging_travel_group_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python emerging_travel_group_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    You need to provide an API key, which you can obtain from the API service, and include it in your request using the format '<KEY_ID>:<API_KEY>' in the curl command.
    
    To get the appropriate API keys, please visit the original source at https://docs.emergingtravel.com/docs/b2b-api/endpoints/retrieve-endpoints/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python emerging_travel_group_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline emerging_travel_group load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset emerging_travel_group_data
    The duckdb destination used duckdb:/emerging_travel_group.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline emerging_travel_group_pipeline show
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("emerging_travel_group_pipeline").dataset()
   # get "finish" table as Pandas frame
   data."finish".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
