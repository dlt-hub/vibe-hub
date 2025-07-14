In this guide, we'll set up a complete Bubble data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def bubble_instance_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-bubble-instance.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "user/email",
            "create_thing",
            "delete_thing"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='bubble_instance_pipeline',
        destination='duckdb',
        dataset_name='bubble_instance_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(bubble_instance_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from bubble_instance’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- User Management: Provides endpoints like retrieving user email, login status, account existence, etc.
- Experiment Management: Endpoints to handle Google Optimize experiments such as getting experiment groups or setting experiments on pages.
- Data Management: Includes creating, modifying, and deleting objects as well as handling CSV uploads and downloads.

You can combine these endpoints to build pipelines that extract structured content from Bubble workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@bubble_instance-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Bubble support.
    ```shell
    dlt init dlthub:bubble_instance duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@bubble_instance-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Bubble API, as specified in @bubble_instance-docs.yaml 
    Start with endpoints "user/email" and "create_thing" and skip incremental loading for now. 
    Place the code in bubble_instance_pipeline.py and name the pipeline bubble_instance_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python bubble_instance_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The authentication method is OAuth2, requiring a client ID, client secret, and a token URL for obtaining access tokens. Tokens are passed in the header under the name 'Authorization'.
    
    To get appropriate API keys, please visit the original source at https://bubble.io/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python bubble_instance_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline bubble_instance load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset bubble_instance_data
    The duckdb destination used duckdb:/bubble_instance.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline bubble_instance_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("bubble_instance_pipeline").dataset()
   # get "user/email" table as Pandas frame
   data."user/email".df().head()
    ```

## Running into errors?

Authentication requires careful setup of OAuth2 with specific token and refresh URLs. Be aware of API rate limits, especially when using shared resources on non-dedicated plans. The API is only accessible on paid plans, and careful attention must be paid to domain settings when setting up integrations. Special characters in passwords must be URL-encoded, and there are limitations on list sizes and SQL query formats.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)