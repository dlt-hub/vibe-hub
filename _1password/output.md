In this guide, we'll set up a complete 1Password Connect data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def onepassword_connect_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.api-name.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "vaults",
            "items",
            "user_access_management"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='onepassword_connect_pipeline',
        destination='duckdb',
        dataset_name='onepassword_connect_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(onepassword_connect_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from onepassword_connect’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Vault Management: Manage vaults and their associated permissions.
- Item Management: Manage items stored within vaults, including creation, retrieval, and updates.
- User Access Management: Control and audit user access to various resources within 1Password.
- Compliance Reporting: Generate compliance reports for auditing and tracking.
- Account Management: Handles operations related to partner accounts, including creation, deletion, and updates.

You can combine these endpoints to build pipelines that extract structured content from 1Password Connect workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@onepassword_connect-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with 1Password Connect support.
    ```shell
    dlt init dlthub:onepassword_connect duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@onepassword_connect-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for 1Password Connect API, as specified in @onepassword_connect-docs.yaml 
    Start with endpoints "vaults" and "items" and skip incremental loading for now. 
    Place the code in onepassword_connect_pipeline.py and name the pipeline onepassword_connect_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python onepassword_connect_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    1Password uses OAuth2 with refresh token flow for authentication. The token is passed in the Authorization header as a Bearer token.
    
    To get appropriate API keys, please visit the original source at https://1password.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python onepassword_connect_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline onepassword_connect load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset onepassword_connect_data
    The duckdb destination used duckdb:/onepassword_connect.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline onepassword_connect_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("onepassword_connect_pipeline").dataset()
   # get "vaults" table as Pandas frame
   data."vaults".df().head()
    ```

## Running into errors?

Ensure to handle sensitive information securely, avoiding logging sensitive data. Be aware of the SDK version and compatibility, especially during upgrades that might introduce breaking changes. Service accounts and their permissions need careful management to adhere to the principle of least privilege. Be cautious of the maximum message size limitations in SDK operations.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)