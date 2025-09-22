In this guide, we'll set up a complete Cred Protocol data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def cred_protocol_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://beta.credprotocol.com/api/token/auth",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "create", "verify"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='cred_protocol_pipeline',
        destination='duckdb',
        dataset_name='cred_protocol_data', 
    )
    # Load the data
    load_info = pipeline.run(cred_protocol_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from cred_protocol’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Score Endpoints**: Endpoints related to scoring based on addresses or identities.
  - `/api/sandbox/score/address/{address}`: Sandbox endpoint for scoring a specific address.
  - `/api/score/history/address/{address}`: Retrieves scoring history for a specific address.
  - `/api/score/address/{account_address}`: Scores based on a specific account address.

- **Recommendation Endpoints**: Endpoints for generating recommendations based on addresses.
  - `/api/recommendation/address/{address}`: Provides recommendations for a specific address.
  - `/api/recommendation/address/{account_address}`: Provides recommendations based on a specific account address.

- **Authentication Endpoints**: Endpoints for token creation and verification.
  - `/api/token/auth/create`: Creates an authentication token.
  - `/api/token/auth/verify`: Verifies an existing authentication token.

- **Similarity Endpoints**: Endpoints for finding similarities based on addresses.
  - `/api/similarity/address/{account_address}`: Analyzes similarities related to a specific account address.
  - `/api/similarity/address/{address}`: Analyzes similarities related to a specific address.

- **Asset Endpoints**: Endpoints for accessing asset-related information.
  - `/api/asset/address/{account_address}`: Retrieves asset information related to a specific account address.
  - `/api/asset/history/address/{address}`: Retrieves asset history for a specific address.

- **Sanction Endpoints**: Endpoints for checking sanctions related to account addresses.
  - `/api/sanction/address/{account_address}`: Checks for sanctions associated with a specific account address.

- **Report Endpoints**: Endpoints for generating reports based on addresses.
  - `/api/report/address/{address}`: Generates a report for a specific address.

- **Summary Endpoints**: Endpoints for summarizing information related to addresses.
  - `/api/sandbox/summary/address/{address}`: Sandbox endpoint for summarizing information on a specific address.
  - `/api/sandbox/summary/address/{account_address}`: Sandbox endpoint for summarizing information on a specific account address.

You will then debug the Cred Protocol pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Cred Protocol support.
    ```shell
    dlt init dlthub:cred_protocol duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Cred Protocol API, as specified in @cred_protocol-docs.yaml 
    Start with endpoints create and verify and skip incremental loading for now. 
    Place the code in cred_protocol_pipeline.py and name the pipeline cred_protocol_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python cred_protocol_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    The snippets mention the requirement for a token to be created, refreshed, and verified through various post methods such as "postCreate Token Auth Credentials," "postRefresh Token Auth Credentials," and "postVerify Token Auth Credentials," as well as through JSON Web Tokens (JWT) using "postCreate JSON Web Tokens (JWT) Auth Credentials," "postRefresh JSON Web Tokens (JWT) Auth Credentials," and "postVerify JSON Web Tokens (JWT) Auth Credentials."
    
    To get the appropriate API keys, please visit the original source at https://beta.credprotocol.com/docs/api.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python cred_protocol_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline cred_protocol load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset cred_protocol_data
    The duckdb destination used duckdb:/cred_protocol.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline cred_protocol_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("cred_protocol_pipeline").dataset()
   # get "create" table as Pandas frame
   data."create".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)