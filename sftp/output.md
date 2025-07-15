In this guide, we'll set up a complete SFTP Gateway data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def sftp_gateway_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "http://sftpgateway.com/api/v3.6.0/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "users",
            "cloudconnections",
            "sftp/configuration"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='sftp_gateway_pipeline',
        destination='duckdb',
        dataset_name='sftp_gateway_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(sftp_gateway_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from sftp_gateway’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- User Management: Manage SFTP users, including creation, deletion, and configuration.
- Cloud Connections: Handle configurations related to cloud services like AWS S3.
- System Settings: Modify system-wide settings, including security configurations and authentication methods.

You can combine these endpoints to build pipelines that extract structured content from SFTP Gateway workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@sftp_gateway-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with SFTP Gateway support.
    ```shell
    dlt init dlthub:sftp_gateway duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@sftp_gateway-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for SFTP Gateway API, as specified in @sftp_gateway-docs.yaml 
    Start with endpoints "users" and "cloudconnections" and skip incremental loading for now. 
    Place the code in sftp_gateway_pipeline.py and name the pipeline sftp_gateway_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python sftp_gateway_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The SFTP Gateway supports OAuth2 for authentication, utilizing a token-based system with refresh capabilities. An OAuth token can be obtained through a specific token URL provided in the client setup.
    
    To get appropriate API keys, please visit the original source at https://www.thorntech.com/sftp-gateway/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python sftp_gateway_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline sftp_gateway load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset sftp_gateway_data
    The duckdb destination used duckdb:/sftp_gateway.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline sftp_gateway_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("sftp_gateway_pipeline").dataset()
   # get "users" table as Pandas frame
   data."users".df().head()
    ```

## Running into errors?

Ensure proper configuration of security settings such as disabling insecure algorithms and using strong key types for SSH. Monitor and limit IP access to avoid unauthorized entry. Regularly update the system to maintain security and functionality. Be aware of the specifics regarding port usage and ensure proper firewall settings to allow necessary traffic while blocking potentially harmful connections.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)