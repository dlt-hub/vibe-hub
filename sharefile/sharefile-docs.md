In this guide, we'll set up a complete ShareFile data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def share_file_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://mycompany.sf-api.com/sf/v3/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "Items",
            "WebhookSubscriptions",
            "AccessControls"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='share_file_pipeline',
        destination='duckdb',
        dataset_name='share_file_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(share_file_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from share_file’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Items: Manage files and folders. 
- WebhookSubscriptions: Manage webhook subscriptions, including create, retrieve, and delete. 
- Access Controls: Retrieve access control settings. 
- Accounts: Retrieve account information. 
- Async Operations: Manage asynchronous operations. 
- Capabilities: Retrieve system capabilities. 
- Favorite Folders: Manage favorite folders. 
- Groups: Retrieve group information. 
- Sessions: Manage user sessions. 
- Shares: Manage shared files and folders. 
- Storage Centers: Retrieve information about storage centers. 
- Users: Manage user information.

You can combine these endpoints to build pipelines that extract structured content from ShareFile workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@share_file-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with ShareFile support.
    ```shell
    dlt init dlthub:share_file duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@share_file-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for ShareFile API, as specified in @share_file-docs.yaml 
    Start with endpoints "Items" and "WebhookSubscriptions" and skip incremental loading for now. 
    Place the code in share_file_pipeline.py and name the pipeline share_file_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python share_file_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is handled via OAuth 2.0 with the flow type of 'authorization_code'. The authorization is done through a token URL at 'https://secure.sharefile.com/oauth/token?grant_type=authorization_code'. The client credentials (client ID and client secret) are passed in the headers under the 'Authorization' key. The location of the authorization header is specified, and the type of token used is a bearer token.
    
    To get appropriate API keys, please visit the original source at https://www.sharefile.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python share_file_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline share_file load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset share_file_data
    The duckdb destination used duckdb:/share_file.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline share_file_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("share_file_pipeline").dataset()
   # get "Items" table as Pandas frame
   data."Items".df().head()
    ```

## Running into errors?

Excessive usage of the API may lead to programmatic curtailment. Cross-Origin Resource Sharing (CORS) restrictions might prevent requests from different domains. Ensure the Redirect URI is appropriately set for use with tools like Postman. ShareFile implements an exponential back-off in retry strategies to avoid overloading servers. The event's Time-to-Live is 7 days, after which retries may cease.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)