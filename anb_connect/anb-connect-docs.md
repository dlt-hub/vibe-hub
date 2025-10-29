In this guide, we'll set up a complete Sadad data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def sadad_migrations_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.sadad.com/v/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            update,,create,,balance
            ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='sadad_migrations_pipeline',
        destination='duckdb',
        dataset_name='sadad_migrations_data', 
    )
    # Load the data
    load_info = pipeline.run(sadad_migrations_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from sadad_migrations’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Update: Handles updates to various resources.
- Create: Endpoint for creating new entries.
- Balance: Retrieves account balance information.
- Inquiry: General inquiries related to services.
- Payment: Processes payments.
- Fee Inquiry: Inquires about fees for services.
- Bill Inquiry: Checks details about bills.
- Refund Status: Checks the status of refunds.
- Refund Inquiry: Inquires about refunds.
- Status Update: Updates the status of requests.
- Create Profile: Creates new user profiles.
- Payment Inquiry: Inquiries on payment statuses.
- Bill Pay Advice: Provides advice on bill payments.
- Invoice Inquiry: Inquires about invoices.
- General Statement: Retrieves general account statements.

You will then debug the Sadad pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Sadad support.
    ```shell
    dlt init dlthub:sadad_migrations duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Sadad API, as specified in @sadad_migrations-docs.yaml 
    Start with endpoints update and  and skip incremental loading for now. 
    Place the code in sadad_migrations_pipeline.py and name the pipeline sadad_migrations_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python sadad_migrations_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    The authentication method used is OAuth2 with a refresh token flow, which requires setting up a connected app and using client credentials to obtain access tokens.
    
    To get the appropriate API keys, please visit the original source at https://www.sadad.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python sadad_migrations_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline sadad_migrations load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset sadad_migrations_data
    The duckdb destination used duckdb:/sadad_migrations.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline sadad_migrations_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("sadad_migrations_pipeline").dataset()
   # get pdat table as Pandas frame
   data.pdat.df().head()
    ```

## Running into errors?

Access to this service requires formal approval from the Saudi Central Bank, and specific endpoints may need to be whitelisted. Additionally, files must be correctly formatted as CSV; otherwise, they will be rejected. There are limits on the number of records, and clients need to be connected to ANB’s B2B platform for certain services.

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
