In this guide, we'll set up a complete Guestonline data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def guestonline_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.guestonline.fr/v1/pending_bookings/55cc4c049089c76870000001",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "commit", "payment_confirmed"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='guestonline_pipeline',
        destination='duckdb',
        dataset_name='guestonline_data', 
    )
    # Load the data
    load_info = pipeline.run(guestonline_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from guestonline’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Pending Bookings**: Endpoints related to managing and modifying pending bookings, including adding or removing offers and vouchers, committing bookings, and confirming payments.
- **Bookings**: Endpoints for managing confirmed bookings, including viewing specific booking details.
- **Commercials**: Endpoints that provide information on commercial entities or offers.
- **Areas**: Endpoints that return information about different areas or locations.
- **Contacts**: Endpoints for accessing and managing contact information.
- **Module Settings**: Endpoints that deal with various configuration settings for modules within the system.
- **Runtime Services**: Endpoints that handle opening and closing of runtime services, possibly for managing sessions or temporary operations.
- **Restaurants**: Endpoints that provide information about restaurants, likely including their details and offerings.
- **Periods**: Endpoints that may relate to timeframes or scheduling within the system.

You will then debug the Guestonline pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Guestonline support.
    ```shell
    dlt init dlthub:guestonline duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Guestonline API, as specified in @guestonline-docs.yaml 
    Start with endpoints commit and payment_confirmed and skip incremental loading for now. 
    Place the code in guestonline_pipeline.py and name the pipeline guestonline_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python guestonline_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate with the Guestonline API, you need to use an API key, which you can obtain from the TableOnline team; make sure to keep it secret and apply it in the request header as "X-Token: your_token", replacing "your_token" with your actual API key.
    
    To get the appropriate API keys, please visit the original source at http://gol-api-doc.s3-website-eu-west-1.amazonaws.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python guestonline_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline guestonline load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset guestonline_data
    The duckdb destination used duckdb:/guestonline.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline guestonline_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("guestonline_pipeline").dataset()
   # get "commit" table as Pandas frame
   data."commit".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
