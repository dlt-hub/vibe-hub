In this guide, we'll set up a complete DomainTools data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def domain_tools_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.domaintools.com/v1/iris-detect/domains",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "ignored", "new"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='domain_tools_pipeline',
        destination='duckdb',
        dataset_name='domain_tools_data', 
    )
    # Load the data
    load_info = pipeline.run(domain_tools_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from domain_tools’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Account Management**: Endpoints related to account information and settings.
  - `/v1/account`: Access account details.

- **Domain Tools**: Endpoints for querying domain information and enrichment.
  - `/v1/domaintools.com?api_username=example&api_key=xxxxxx`: Retrieve information from DomainTools using API credentials.

- **Feed Management**: Endpoints for managing and retrieving feeds of domains or updates.
  - `/v1/feed/nod/?after=-60`: Get feed updates for the specified time period.

- **Domain Detection**: Endpoints for managing and querying detected domains (watched, ignored, new).
  - `/v1/iris-detect/domains/watched`: List domains that are being actively monitored.
  - `/v1/iris-detect/domains/ignored/?api_username=`: Retrieve ignored domains.

- **Domain Investigation**: Endpoints for investigating specific domains with additional information.
  - `/v1/iris-investigate/?domain=example.com&screenshots=1`: Investigate a specific domain with optional screenshots.

- **Monitoring**: Endpoints for managing and querying domain monitoring activities.
  - `/v1/iris-detect/monitors`: List all monitors and their statuses.
  - `/v1/iris-detect/monitors/?datetime_counts_since=2022-01-14%2016:27:31&include_counts=true`: Get monitor counts since a specific date.

- **Domain Escalations**: Endpoints for managing escalations related to domains.
  - `/v1/iris-detect/escalations/?api_username=`: Retrieve escalation information for a specific user.

- **Newly Detected Domains**: Endpoints specifically for querying newly discovered domains.
  - `/v1/iris-detect/domains/new`: List newly detected domains.
  - `/v1/iris-detect/domains/new/?discovered_since=2022-01-20%2009:40:12.000000%20-08:00&api_username=`: Query newly discovered domains since a specified date.

You will then debug the DomainTools pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with DomainTools support.
    ```shell
    dlt init dlthub:domain_tools duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for DomainTools API, as specified in @domain_tools-docs.yaml 
    Start with endpoints ignored and new and skip incremental loading for now. 
    Place the code in domain_tools_pipeline.py and name the pipeline domain_tools_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python domain_tools_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    The Iris API requires an API key, which can be obtained through the DomainTools account and should be included in the request header for authentication.
    
    To get the appropriate API keys, please visit the original source at https://docs.domaintools.com/api/iris/enrich/reference/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python domain_tools_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline domain_tools load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset domain_tools_data
    The duckdb destination used duckdb:/domain_tools.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline domain_tools_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("domain_tools_pipeline").dataset()
   # get "ignored" table as Pandas frame
   data."ignored".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
