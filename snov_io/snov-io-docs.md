In this guide, we'll set up a complete Snov.io data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def snov_io_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.snov.io/v2/domain-search/prospects/search-emails",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "result", "start"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='snov_io_pipeline',
        destination='duckdb',
        dataset_name='snov_io_data', 
    )
    # Load the data
    load_info = pipeline.run(snov_io_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from snov_io’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Domain Search**: Endpoints related to searching and retrieving domain-related information and email results.
  - `/v2/domain-search/domain-emails/result/$task_hash`: Fetches the results of a domain email search using a task hash.
  - `/v2/domain-search/start`: Initiates a domain search process.
  - `/v2/domain-search/prospects/result/$task_hash`: Retrieves prospects based on a domain search task hash.
  - `/v2/domain-search/prospects/search-emails/start`: Starts the process of searching for emails associated with prospects.
  
- **Campaign Management**: Endpoints for managing and retrieving information about user campaigns.
  - `/v1/get-user-campaigns`: Fetches all campaigns associated with the user.
  - `/v2/campaigns/$campaignId/progress?`: Retrieves the progress of a specific campaign by its ID.
  - `/v2/campaigns/{campaign_id`: (Possible typo in endpoint) Used for accessing specific campaign details.

- **User Lists and Profiles**: Endpoints to manage user-specific lists and profiles.
  - `/v1/get-user-lists?`: Retrieves lists created by the user.
  - `/v1/get-profile-by-email`: Fetches user profile information using an email address.

- **Email Verification**: Endpoint for verifying email addresses.
  - `/v2/email-verification/start`: Initiates the email verification process.

- **Webhooks**: Endpoints for managing webhooks.
  - `/v2/webhooks/webhook_id`: Accesses specific webhook information.
  - `/v2/webhooks/8`: Fetches details for a specific webhook identified by ID.

- **Do Not Email List**: Endpoint for managing email exclusions.
  - `/v1/do-not-email-list`: Retrieves or manages the list of emails that should not be contacted. 

- **Prospect Lists**: Endpoint for managing prospect lists.
  - `/v1/prospect-list`: Accesses the user's prospect lists.

You will then debug the Snov.io pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Snov.io support.
    ```shell
    dlt init dlthub:snov_io duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Snov.io API, as specified in @snov_io-docs.yaml 
    Start with endpoints result and start and skip incremental loading for now. 
    Place the code in snov_io_pipeline.py and name the pipeline snov_io_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python snov_io_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate future requests with the Snov.io API, you need to generate an access token by making a POST request to the token URL [https://api.snov.io/v1/oauth/access_token](#) with the grant type set to client_credentials, and then specify this access token in the Authorization field as "Authorization: Bearer <your_access_token>", where <your_access_token> is the token you receive; you can find your client id and client secret in the account settings at <https://app.snov.io/account/api>.
    
    To get the appropriate API keys, please visit the original source at https://snov.io/api.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python snov_io_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline snov_io load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset snov_io_data
    The duckdb destination used duckdb:/snov_io.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline snov_io_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("snov_io_pipeline").dataset()
   # get "result" table as Pandas frame
   data."result".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
