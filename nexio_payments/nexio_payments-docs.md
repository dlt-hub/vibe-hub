In this guide, we'll set up a complete Nexio data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def nexio_payments_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.nexiopay.com/",
            "auth": {
                "type": "apikey",
                "token": access_token,
                "header_name": "Authorization"
            },
        },
        "resources": [
            "terminals",
            "recipients",
            "payment_types"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='nexio_payments_pipeline',
        destination='duckdb',
        dataset_name='nexio_payments_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(nexio_payments_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from nexio_payments’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Terminal Management: Manage and list terminal devices.
- Recipient Management: Handle recipient data and details.
- Payment Type Management: Manage and retrieve types of payment methods.
- User Management: Operations related to users such as retrieving user information.
- Spendbacks Management: Manage and retrieve spendback information.
- Token Management: Manage and retrieve card tokens.

You can combine these endpoints to build pipelines that extract structured content from Nexio workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@nexio_payments-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Nexio support.
    ```shell
    dlt init dlthub:nexio_payments duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Nexio API, as specified in @nexio_payments-docs.yaml 
    Start with endpoints "terminals" and "recipients" and skip incremental loading for now. 
    Place the code in nexio_payments_pipeline.py and name the pipeline nexio_payments_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python nexio_payments_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done using an API key and API username. The API key must be included in the headers under 'Authorization'.
    
    To get appropriate API keys, please visit the original source at https://nexiopay.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python nexio_payments_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline nexio_payments load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset nexio_payments_data
    The duckdb destination used duckdb:/nexio_payments.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline nexio_payments_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("nexio_payments_pipeline").dataset()
   # get terminals table as Pandas frame
   data.terminals.df().head()
    ```

## Running into errors?

Ensure that the API Username and API Key are correctly set up in Nexio Dashboard. Be aware of rate limits on endpoints and implement retry mechanisms with exponential backoff for handling '429 Too Many Requests' errors. Properly handle possible '401 Unauthorized' by verifying API keys and username, and '404 Not Found' by checking endpoint paths and parameters.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)