In this guide, we'll set up a complete PayPal data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def paypal_sandbox_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://www.sandbox.paypal.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "disputes",
            "services/data/vXX.X/sobjects/CampaignMember",
            "donate/sdk/donate-sdk.js"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='paypal_sandbox_pipeline',
        destination='duckdb',
        dataset_name='paypal_sandbox_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(paypal_sandbox_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from paypal_sandbox’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Dispute Management: Manage and resolve disputes, including details, messages, offers, escalations, evidence, claims, and appeals.
- Data Access: Access to Salesforce objects like Campaign Members and Contacts via RESTful services.
- Payment Widgets: Integration of payment functionalities like the donate button directly into web applications.

You can combine these endpoints to build pipelines that extract structured content from PayPal workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@paypal_sandbox-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with PayPal support.
    ```shell
    dlt init dlthub:paypal_sandbox duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@paypal_sandbox-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for PayPal API, as specified in @paypal_sandbox-docs.yaml 
    Start with endpoints "disputes" and "services/data/vXX.X/sobjects/CampaignMember" and skip incremental loading for now. 
    Place the code in paypal_sandbox_pipeline.py and name the pipeline paypal_sandbox_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python paypal_sandbox_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is based on OAuth 2.0 with access tokens. A refresh token flow is used, requiring credentials such as client ID, client secret, and refresh token.
    
    To get appropriate API keys, please visit the original source at https://www.paypal.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python paypal_sandbox_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline paypal_sandbox load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset paypal_sandbox_data
    The duckdb destination used duckdb:/paypal_sandbox.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline paypal_sandbox_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("paypal_sandbox_pipeline").dataset()
   # get "disputes" table as Pandas frame
   data."disputes".df().head()
    ```

## Running into errors?

Ensure proper handling of various response codes for error management. Be aware of potential null returns in deeply nested fields. Follow PCI compliance when using PayPal Card Fields for card information collection. Use sandbox environment for testing before production and replace sandbox data with live data for production deployment.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)