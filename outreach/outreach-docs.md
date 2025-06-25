In this guide, we'll set up a complete Outreach data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def outreach_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.outreach.io/api/v2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "prospects",
            "sequences",
            "accounts"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='outreach_pipeline',
        destination='duckdb',
        dataset_name='outreach_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(outreach_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from outreach’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Prospects: Retrieve information about prospects.
- Sequences: Access and manage sequences for automated outreach processes.
- SequenceStates: Manage and retrieve states of sequences.
- SequenceSteps: Access steps within sequences.
- Calls: Retrieve data about calls made within the Outreach platform.
- Mailings: Access mailing activities and details.
- Accounts: Manage and retrieve account information.
- Opportunities: Access opportunities data.
- Personas: Manage and retrieve personas associated with accounts.
- Mailboxes: Access mailbox configurations and details.
- Stages: Retrieve information about different stages in sales or marketing processes.
- Users: Access user details within the Outreach platform.
- Tasks: Manage and retrieve tasks assigned to users.
- Templates: Access and manage communication templates.
- Snippets: Retrieve and manage reusable text snippets for communications.

You can combine these endpoints to build pipelines that extract structured content from Outreach workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@outreach-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Outreach support.
    ```shell
    dlt init dlthub:outreach duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Outreach API, as specified in @outreach-docs.yaml 
    Start with endpoints "prospects" and "sequences" and skip incremental loading for now. 
    Place the code in outreach_pipeline.py and name the pipeline outreach_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python outreach_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed through OAuth 2.0 with a refresh token flow. The client must store the client ID, client secret, and refresh token securely. The token endpoint is used to refresh the access token using these credentials.
    
    To get appropriate API keys, please visit the original source at https://www.outreach.io/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python outreach_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline outreach load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset outreach_data
    The duckdb destination used duckdb:/outreach.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline outreach_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("outreach_pipeline").dataset()
   # get prospects table as Pandas frame
   data.prospects.df().head()
    ```

## Running into errors?

Authentication requires initial setup with Outreach support to obtain OAuth credentials. The API supports both full refresh and incremental syncs, which should be carefully managed to avoid hitting request limits. Throttling or reducing request frequency might be necessary in case of reaching the API request limit.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)