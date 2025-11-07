In this guide, we'll set up a complete YarsaPlay data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def yarsaplay_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.yarsaplay.com/api/v1/iap/validate",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "apple", "google"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='yarsaplay_pipeline',
        destination='duckdb',
        dataset_name='yarsaplay_data', 
    )
    # Load the data
    load_info = pipeline.run(yarsaplay_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from yarsaplay’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **IAP Validation**: Endpoints for validating in-app purchases from Google and Apple.
  - `/api/v1/iap/validate/google`: Validate Google in-app purchases.
  - `/api/v1/iap/validate/apple`: Validate Apple in-app purchases.

- **Authentication**: Endpoints related to user authentication and account management.
  - `/api/v1/auth/login?provider=Device`: Log in using a device as the provider.
  - `/api/v1/auth/unlink?provider=Device`: Unlink a device from the user's account.
  - `/api/v1/auth/socialLink?provider=Device`: Link a social account to the user's device account.

- **User Profile**: Endpoint to manage user profile information.
  - `/api/v1/profile`: Access or update user profile details.

- **Friendship Management**: Endpoints for managing friendships and related requests.
  - `/api/v1/friendship/search?displayName=`: Search for friends by display name.
  - `/api/v1/friendship/__FRIENDSHIPID__`: Manage specific friendship by ID.
  - `/api/v1/friendship/requests/incoming`: View incoming friendship requests.
  - `/api/v1/friendship/requests/outgoing`: View outgoing friendship requests.
  - `/api/v1/friendship/block`: Block a user.
  
- **Inventory Management**: Endpoints for managing user inventory.
  - `/api/v1/inventory`: Access user inventory details.
  - `/api/v1/inventory/__ID__/deduct`: Deduct an item from the inventory. 

- **Asset Management**: Endpoint for accessing specific assets related to applications.
  - `/api/v1/assets/unity/__APPLICATIONID__/__ASSETNAME__`: Access assets for a given application ID and asset name.

You will then debug the YarsaPlay pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with YarsaPlay support.
    ```shell
    dlt init dlthub:yarsaplay duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for YarsaPlay API, as specified in @yarsaplay-docs.yaml 
    Start with endpoints apple and google and skip incremental loading for now. 
    Place the code in yarsaplay_pipeline.py and name the pipeline yarsaplay_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python yarsaplay_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate, you need a Bearer token which can be obtained as a user authentication token, and it should be applied in the request headers with the header name `Authorization`.
    
    To get the appropriate API keys, please visit the original source at https://docs.yarsaplay.com/docs/api/in-app-purchase/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python yarsaplay_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline yarsaplay load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset yarsaplay_data
    The duckdb destination used duckdb:/yarsaplay.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline yarsaplay_pipeline show
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("yarsaplay_pipeline").dataset()
   # get "apple" table as Pandas frame
   data."apple".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
