In this guide, we'll set up a complete Apple Developer Services data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def apple_developer_services_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.apple.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "store/services/tier1",
            "store/services/tier2",
            "transaction/reporting"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='apple_developer_services_pipeline',
        destination='duckdb',
        dataset_name='apple_developer_services_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(apple_developer_services_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from apple_developer_services’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Store Services: Provides access to various tiers of store services. 
- Transaction Reporting: Allows for the reporting of transactions. 
- Developer Tools: Access to tools needed for app development. 
- App Store Metrics: Metrics and analytics for app performance in the store. 
- External Purchase Link Entitlement: Manages external purchase links. 
- App Analytics: Detailed analytics and reports for apps. 
- User Data Portability: Handles the transfer and portability of user data.

You can combine these endpoints to build pipelines that extract structured content from Apple Developer Services workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@apple_developer_services-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Apple Developer Services support.
    ```shell
    dlt init dlthub:apple_developer_services duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@apple_developer_services-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Apple Developer Services API, as specified in @apple_developer_services-docs.yaml 
    Start with endpoints "store/services/tier1" and "store/services/tier2" and skip incremental loading for now. 
    Place the code in apple_developer_services_pipeline.py and name the pipeline apple_developer_services_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python apple_developer_services_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed through OAuth2 with token refresh capabilities. The client ID, client secret, and refresh token are stored securely in secrets management and used to obtain the bearer token.
    
    To get appropriate API keys, please visit the original source at https://www.apple.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python apple_developer_services_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline apple_developer_services load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset apple_developer_services_data
    The duckdb destination used duckdb:/apple_developer_services.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline apple_developer_services_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("apple_developer_services_pipeline").dataset()
   # get "store/services/tier1" table as Pandas frame
   data."store/services/tier1".df().head()
    ```

## Running into errors?

Developers need to ensure they comply with notarization requirements, manage payment and billing issues, comply with the Digital Markets Act, and are responsible for the code included by third-party SDKs. Additionally, developers must handle taxes, respect user privacy, and ensure compatibility with specified device and OS versions.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)