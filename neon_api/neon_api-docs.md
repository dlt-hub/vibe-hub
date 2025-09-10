In this guide, we'll set up a complete Neon Database data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def neon_database_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://console.neon.tech/api/v2/",
            "auth": {
                "type": "apikey",
                "location": "header",
                "key": api_key,
            },
        },
        "resources": [
            "auth",
            "",
            "regions",
            "",
            "users/me"
            ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='neon_database_pipeline',
        destination='duckdb',
        dataset_name='neon_database_data', 
    )
    # Load the data
    load_info = pipeline.run(neon_database_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from neon_database’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Authentication: Manage API keys and user authentication
- Projects: Create and manage database projects with branches and endpoints
- Organizations: Handle organization-level access and shared projects
- Infrastructure: Manage regions, snapshots, and database operations
- Monitoring: Track consumption metrics and usage history

You will then debug the Neon Database pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Neon Database support.
    ```shell
    dlt init dlthub:neon_database duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Neon Database API, as specified in @neon_database-docs.yaml 
    Start with endpoints "auth" and "" and skip incremental loading for now. 
    Place the code in neon_database_pipeline.py and name the pipeline neon_database_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python neon_database_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    Neon uses API key authentication with multiple key types including Personal API keys for personal projects, Organization API keys for full admin access, and Project-scoped organization API keys for member-level access to specific projects. Keys are 64-bit randomly-generated tokens that remain valid until revoked and must be included in API requests.
    
    To get the appropriate API keys, please visit the original source at https://neon.tech/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python neon_database_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline neon_database load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset neon_database_data
    The duckdb destination used duckdb:/neon_database.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline neon_database_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("neon_database_pipeline").dataset()
   # get auth table as Pandas frame
   data.auth.df().head()
    ```

## Running into errors?

The API has strict rate limiting of 700 requests per minute with bursts up to 40 requests per second per route per account, and applications must handle 429 errors appropriately. Currently only AWS platform is supported, consumption history has time-based limitations depending on granularity, and some endpoints are only available for Scale, Business, and Enterprise plan accounts. Azure regions are not supported and deleted VPC endpoints cannot be re-added.

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)