In this guide, we'll set up a complete Optimove data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def optimove_data_connections_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api4.optimove.net/Triggers",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "UnregisterEventListener", "RegisterEventListener"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='optimove_data_connections_pipeline',
        destination='duckdb',
        dataset_name='optimove_data_connections_data', 
    )
    # Load the data
    load_info = pipeline.run(optimove_data_connections_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from optimove_data_connections’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Triggers**: 
  - `/Triggers/RegisterEventListener`: Registers an event listener for specific triggers.
  - `/Triggers/UnregisterEventListener`: Unregisters an event listener for specific triggers.

- **Conditional Execution**: 
  - `/conditional-execution/v2/triggered/approval`: Handles approval processes triggered by conditions.

- **Transactional Mail**: 
  - `/TransactionalMail/SendTransactionalMail`: Sends a standard transactional email.
  - `/TransactionalMail/SendFinalizedTransactionalMail`: Sends a finalized version of a transactional email.

- **Optimail**: 
  - `/Optimail/AddTemplate`: Adds a new email template for use.
  - `/Optimail/UpdateTemplate`: Updates an existing email template.
  - `/Optimail/AddUnsubscribers`: Adds users to the unsubscribed list.
  - `/Optimail/DeleteUnsubscribers`: Removes users from the unsubscribed list.

- **Integrations**: 
  - `/Integrations/AddPromotions`: Adds new promotional offers.
  - `/Integrations/DeletePromotions`: Removes promotional offers.
  - `/Integrations/SetCustomerChannelPreference`: Sets customer preferences for communication channels.
  - `/Integrations/UpdateCampaignMetrics`: Updates metrics for marketing campaigns.
  - `/Integrations/AddChannelTemplates`: Adds templates for communication channels.
  - `/Integrations/DeleteChannelTemplates`: Deletes templates for communication channels.
  - `/Integrations/AddChannelApps`: Adds applications for communication channels.
  - `/Integrations/DeleteChannelApps`: Deletes applications for communication channels.
  - `/Integrations/UpdateCustomerPromotionStatus`: Updates the promotional status of customers.

- **Customers**: 
  - `/Customers/RemoveCustomerPII`: Removes personally identifiable information from customer records.

You will then debug the Optimove pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Optimove support.
    ```shell
    dlt init dlthub:optimove_data_connections duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Optimove API, as specified in @optimove_data_connections-docs.yaml 
    Start with endpoints UnregisterEventListener and RegisterEventListener and skip incremental loading for now. 
    Place the code in optimove_data_connections_pipeline.py and name the pipeline optimove_data_connections_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python optimove_data_connections_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    The API requires an API key, which can be generated by following the instructions in the [Generating API Keys](https://example.com/reference/generating-api-keys-2) guide.
    
    To get the appropriate API keys, please visit the original source at https://developer.optimove.com/docs/data-connections-api.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python optimove_data_connections_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline optimove_data_connections load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset optimove_data_connections_data
    The duckdb destination used duckdb:/optimove_data_connections.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline optimove_data_connections_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("optimove_data_connections_pipeline").dataset()
   # get "UnregisterEventListener" table as Pandas frame
   data."UnregisterEventListener".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)