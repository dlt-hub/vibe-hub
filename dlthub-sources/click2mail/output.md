In this guide, we'll set up a complete Click2Mail data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def click2mail_mailing_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.api-name.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "credit_purchase",
            "documents",
            "address_lists"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='click2mail_mailing_pipeline',
        destination='duckdb',
        dataset_name='click2mail_mailing_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(click2mail_mailing_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from click2mail_mailing’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Document Management: Handling of document upload, merging, and management.
- Job Submission: Creation and submission of print and mail jobs.
- Address Management: Creation and verification of address lists, and retrieving address statuses.
- Tracking and Status: Provides endpoints for job tracking and status updates.

You can combine these endpoints to build pipelines that extract structured content from Click2Mail workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@click2mail_mailing-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Click2Mail support.
    ```shell
    dlt init dlthub:click2mail_mailing duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@click2mail_mailing-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Click2Mail API, as specified in @click2mail_mailing-docs.yaml 
    Start with endpoints "credit_purchase" and "documents" and skip incremental loading for now. 
    Place the code in click2mail_mailing_pipeline.py and name the pipeline click2mail_mailing_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python click2mail_mailing_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses OAuth2 authentication with a refresh token flow. The token and client credentials are stored in environment variables and used to obtain access tokens.
    
    To get appropriate API keys, please visit the original source at https://www.click2mail.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python click2mail_mailing_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline click2mail_mailing load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset click2mail_mailing_data
    The duckdb destination used duckdb:/click2mail_mailing.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline click2mail_mailing_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("click2mail_mailing_pipeline").dataset()
   # get "credit_purchase" table as Pandas frame
   data."credit_purchase".df().head()
    ```

## Running into errors?

Tracking information updates once per day. In staging, no tracking details are returned. Only specific file formats are supported. Print options are being deprecated for API direct settings, favoring web interface templates. Batch processing follows FIFO. Various detailed errors require careful handling.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)