In this guide, we'll set up a complete CloudConvert data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def cloud_convert_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://s.cloudconvert.com/v2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "export_url",
            "jobs",
            "pdf_convert"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='cloud_convert_pipeline',
        destination='duckdb',
        dataset_name='cloud_convert_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(cloud_convert_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from cloud_convert’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Export Operations: Covers all export features like exporting to URL, S3, Azure Blob, Google Cloud, OpenStack, and SFTP.
- Job Management: Includes endpoints to create, show, list, and delete conversion jobs.
- PDF Operations: Includes various PDF manipulation tasks like converting, OCR, encrypting, decrypting, splitting, and extracting pages.
- Miscellaneous: Includes endpoints for tasks like archiving and adding watermarks.

You can combine these endpoints to build pipelines that extract structured content from CloudConvert workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@cloud_convert-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with CloudConvert support.
    ```shell
    dlt init dlthub:cloud_convert duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@cloud_convert-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for CloudConvert API, as specified in @cloud_convert-docs.yaml 
    Start with endpoints "export_url" and "jobs" and skip incremental loading for now. 
    Place the code in cloud_convert_pipeline.py and name the pipeline cloud_convert_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python cloud_convert_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done using an API key. The key must be included in the header of each request under the name 'Authorization'.
    
    To get appropriate API keys, please visit the original source at https://www.cloudconvert.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python cloud_convert_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline cloud_convert load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset cloud_convert_data
    The duckdb destination used duckdb:/cloud_convert.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline cloud_convert_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("cloud_convert_pipeline").dataset()
   # get "export_url" table as Pandas frame
   data."export_url".df().head()
    ```

## Running into errors?

Tasks time out after 5 hours by default, and export URLs are valid for only 24 hours. Redirects are supported, and it is essential to validate the cryptographic signature of webhook requests for security. The API operates asynchronously by default, notifying via webhooks upon job completion. Synchronous operations are also available but marked as experimental. Immediate file deletion is possible, and all telemetry data is deleted 72 hours after account deletion.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)