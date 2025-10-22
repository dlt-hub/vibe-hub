In this guide, we'll set up a complete SMSEagle data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def smseagle_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://url-of-smseagle/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "jsonrpc"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='smseagle_pipeline',
        destination='duckdb',
        dataset_name='smseagle_data', 
    )
    # Load the data
    load_info = pipeline.run(smseagle_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from smseagle’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **SMS Endpoints**: 
  - `/index.php/api/v2/messages/sms`: Endpoint for sending SMS messages.
  - `/http_api/read_sms`: Endpoint for reading SMS messages.
  - `/http_api/get_inbox_length`: Endpoint to retrieve the number of messages in the inbox.
  - `/http_api/get_sentitems_length`: Endpoint to retrieve the number of sent messages.

- **Contact Management**: 
  - `/http_api/contact_create`: Endpoint for creating a new contact.
  - `/http_api/contact_read`: Endpoint for reading contact details.
  - `/http_api/contact_update`: Endpoint for updating an existing contact.
  - `/http_api/set_contact_vacation_mode`: Endpoint to set a contact's vacation mode.

- **Group Management**: 
  - `/http_api/group_create`: Endpoint for creating a new user group.
  - `/http_api/send_togroup`: Endpoint for sending messages to a specific group.

- **Shift Management**: 
  - `/http_api/shift_create`: Endpoint for creating a new work shift.
  - `/http_api/shift_update`: Endpoint for updating an existing work shift.
  - `/http_api/shift_delete`: Endpoint for deleting a work shift.

- **Call Management**: 
  - `/http_api/call_forward_read`: Endpoint to read call forwarding settings.
  - `/http_api/call_forward_set`: Endpoint for setting call forwarding.
  - `/http_api/call_with_termination`: Endpoint for initiating a call with termination.

- **USSD Services**: 
  - `/http_api/send_ussd`: Endpoint for sending USSD messages.

- **Text-to-Speech**: 
  - `/http_api/tts_adv_call`: Endpoint for making advanced calls with text-to-speech functionality.

You will then debug the SMSEagle pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with SMSEagle support.
    ```shell
    dlt init dlthub:smseagle duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for SMSEagle API, as specified in @smseagle-docs.yaml 
    Start with endpoint jsonrpc and skip incremental loading for now. 
    Place the code in smseagle_pipeline.py and name the pipeline smseagle_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python smseagle_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate with the SMSEagle API v.2, you need to generate an API access token by selecting **APIv2** and clicking the **Generate new token** button, then include this token as an API key in a HTTP request header (recommended option) or as a query parameter when making requests to the API endpoint at **YOUR-SMSEAGLE-DEVICE-URL/api/v2/{method}**.
    
    To get the appropriate API keys, please visit the original source at https://www.smseagle.eu/api/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python smseagle_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline smseagle load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset smseagle_data
    The duckdb destination used duckdb:/smseagle.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline smseagle_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("smseagle_pipeline").dataset()
   # get "jsonrpc" table as Pandas frame
   data."jsonrpc".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
