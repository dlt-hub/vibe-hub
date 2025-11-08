In this guide, we'll set up a complete ASIC Company Lookup data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def asic_company_lookup_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.vigil.sh/geo/postcode",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "find", "locate"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='asic_company_lookup_pipeline',
        destination='duckdb',
        dataset_name='asic_company_lookup_data', 
    )
    # Load the data
    load_info = pipeline.run(asic_company_lookup_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from asic_company_lookup’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **ASIC Company Endpoints**: Access information related to companies registered with the Australian Securities and Investments Commission (ASIC).
  - `/asic/company/find/?query={query}`: Find company details based on a search query.
  - `/asic/company/?id={id}`: Retrieve specific company information using its ID.

- **Business Industry Code Endpoints**: Access information about business industry classifications.
  - `/ato/business-industry-code/find/?query={query}`: Find business industry codes based on a search query.

- **ABR Business Number Endpoints**: Access Australian Business Register (ABR) information.
  - `/abr/business/number/?id={id}`: Retrieve business information using the business number ID.
  - `/abr/business/number/find/?query={query}`: Find business information based on a search query.

- **APN Bank State Branch Endpoints**: Access information about bank state branches.
  - `/apn/bank-state-branch/?id={id}`: Retrieve specific bank state branch information using its ID.
  - `/apn/bank-state-branch/find/?query={query}`: Find bank state branches based on a search query.

- **ASIC Financial Services Endpoints**: Access information regarding financial services licenses and representatives.
  - `/asic/australian-financial-services/licensee/find/?query={query}`: Find licensees based on a search query.
  - `/asic/australian-financial-services/licensee/?id={id}`: Retrieve specific licensee information using its ID.
  - `/asic/australian-financial-services/authorised-representative/find/?query={query}`: Find authorized representatives based on a search query.
  - `/asic/australian-financial-services/authorised-representative/?id={id}`: Retrieve specific authorized representative information using its ID.

- **ASIC Banned Entities Endpoints**: Access information regarding banned organizations and individuals.
  - `/asic/banned/organisation/find/?query={query}`: Find banned organizations based on a search query.
  - `/asic/banned/organisation/?id={id}`: Retrieve specific banned organization information using its ID.
  - `/asic/banned/person/?id={id}`: Retrieve specific banned person information using their ID.
  - `/asic/banned/person/find/?query={query}`: Find banned persons based on a search query.

- **ASIC Business Name Endpoints**: Access information regarding registered business names.
  - `/asic/business/name/find/?query={query}`: Find business names based on a search query.
  - `/asic/business/name/?id={id}`: Retrieve specific business name information using its ID.

- **ASIC Credit Endpoints**: Access information related to credit licensees and representatives.
  - `/asic/credit/licensee/find/?query={query}`: Find credit licensees based on a search query.
  - `/asic/credit/licensee/?id={id}`: Retrieve specific credit licensee information using its ID.
  - `/asic/credit/representative/find/?query={query}`: Find credit representatives based on a search query.

You will then debug the ASIC Company Lookup pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with ASIC Company Lookup support.
    ```shell
    dlt init dlthub:asic_company_lookup duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for ASIC Company Lookup API, as specified in @asic_company_lookup-docs.yaml 
    Start with endpoints find and locate and skip incremental loading for now. 
    Place the code in asic_company_lookup_pipeline.py and name the pipeline asic_company_lookup_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python asic_company_lookup_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    A valid API key is required, which can be obtained through an active subscription, and it must be included in the header with the name `x-api-key` for the endpoint to work.
    
    To get the appropriate API keys, please visit the original source at https://developer.vigil.sh/api/asic/company/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python asic_company_lookup_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline asic_company_lookup load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset asic_company_lookup_data
    The duckdb destination used duckdb:/asic_company_lookup.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline asic_company_lookup_pipeline show
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("asic_company_lookup_pipeline").dataset()
   # get "find" table as Pandas frame
   data."find".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
