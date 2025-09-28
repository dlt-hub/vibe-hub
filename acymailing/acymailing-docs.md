In this guide, we'll set up a complete AcyMailing data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def acymailing_migration_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://www.acymailing.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            queue,,users,,lists
            ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='acymailing_migration_pipeline',
        destination='duckdb',
        dataset_name='acymailing_migration_data', 
    )
    # Load the data
    load_info = pipeline.run(acymailing_migration_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from acymailing_migration’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Queue: Manages email sending queue
- Posts: Handles email posts
- Users: Manages user data
- Lists: Manages mailing lists
- Emails: Handles email data
- Import: Imports data
- Events: Manages event data
- Delete: Handles deleting operations
- Segment: Manages user segments
- Add Tag: Adds tags to users
- Content: Manages content integration
- Surveys: Manages survey data
- Coupons: Manages coupon data
- Articles: Handles article data
- Contacts: Manages contact information
- Tracking: Manages tracking data
- Segments: Manages user segments
- Text Popup: Handles text popup data

You will then debug the AcyMailing pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with AcyMailing support.
    ```shell
    dlt init dlthub:acymailing_migration duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for AcyMailing API, as specified in @acymailing_migration-docs.yaml 
    Start with endpoints queue and  and skip incremental loading for now. 
    Place the code in acymailing_migration_pipeline.py and name the pipeline acymailing_migration_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python acymailing_migration_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    AcyMailing uses OAuth2 for authentication, which requires setting up a connected app in the API. Users need to manage access tokens and refresh tokens.
    
    To get the appropriate API keys, please visit the original source at https://www.acymailing.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python acymailing_migration_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline acymailing_migration load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset acymailing_migration_data
    The duckdb destination used duckdb:/acymailing_migration.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline acymailing_migration_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("acymailing_migration_pipeline").dataset()
   # get ueu table as Pandas frame
   data.ueu.df().head()
    ```

## Running into errors?

Ensure to follow OAuth2 setup procedures correctly. Some data may not be migratable, and migration can take time depending on data volume. Error handling is crucial, as various errors can occur during operations, including user not found, list not found, and permission issues.

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)