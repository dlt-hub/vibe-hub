In this guide, we'll set up a complete Apeiron data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def apeiron_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.apeiron.io/v2/e911/dynamic",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "address", "locations"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='apeiron_pipeline',
        destination='duckdb',
        dataset_name='apeiron_data', 
    )
    # Load the data
    load_info = pipeline.run(apeiron_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from apeiron’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Configuration Endpoints**: Endpoints related to obtaining and managing configuration settings.
  - `/v2/whitelabel/config`: Retrieves configuration for whitelabel applications.

- **Device Management Endpoints**: Endpoints for managing devices.
  - `/v2/devices`: Retrieves a list of devices.

- **Customer Management Endpoints**: Endpoints related to customer operations.
  - `/v2/customers`: Manages customer data.

- **User Information Endpoints**: Endpoints to access personal user data.
  - `/v2/me`: Retrieves information about the authenticated user.
  - `/v2/me/numbers`: Lists the numbers associated with the authenticated user.

- **E911 Services Endpoints**: Endpoints for managing emergency services.
  - `/v2/e911`: General endpoint for E911 services.
  - `/v2/e911/dynamic/address/delete`: Deletes a dynamic E911 address.
  - `/v2/e911/dynamic/locations/add`: Adds a dynamic E911 location.
  - `/v2/e911/dynamic/locations`: Retrieves dynamic E911 locations.
  - `/v2/e911/dynamic/endpoints/add`: Adds dynamic E911 endpoints.

- **Cellular Services Endpoints**: Endpoints for cellular-related operations.
  - `/v2/cellular/validate`: Validates cellular information.
  - `/v2/cellular/mdns`: Manages mDNS for cellular services.
  - `/v2/cellular/port`: Handles cellular porting requests.

- **Number Management Endpoints**: Endpoints for managing phone numbers.
  - `/v2/numbers/prequal/lnp`: Prequalifies numbers for local number portability.
  - `/v2/numbers/international`: Manages international number services.

- **Ticketing System Endpoints**: Endpoints for managing support tickets.
  - `/v2/tickets`: Handles ticket creation and management.

- **Voicemail Endpoints**: Endpoints for managing voicemail settings.
  - `/v2/voicemail/greetings`: Manages voicemail greetings.

- **Subscriber Management Endpoints**: Endpoints for managing subscribers.
  - `/v2/subscribers/add`: Adds new subscribers.

- **MMS Endpoints**: Endpoints for multimedia messaging services.
  - `/v2/mms`: Manages MMS operations.

- **Authentication Endpoints**: Endpoints for user authentication.
  - `/v2/auth/jwt/refresh`: Refreshes the JSON Web Token for authentication.

You will then debug the Apeiron pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Apeiron support.
    ```shell
    dlt init dlthub:apeiron duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Apeiron API, as specified in @apeiron-docs.yaml 
    Start with endpoints address and locations and skip incremental loading for now. 
    Place the code in apeiron_pipeline.py and name the pipeline apeiron_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python apeiron_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To obtain a JWT access token and a refresh token, use your account credentials through the specified API, with the access token valid for 5 minutes.
    
    To get the appropriate API keys, please visit the original source at https://api.apeiron.io/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python apeiron_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline apeiron load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset apeiron_data
    The duckdb destination used duckdb:/apeiron.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline apeiron_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("apeiron_pipeline").dataset()
   # get "address" table as Pandas frame
   data."address".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)