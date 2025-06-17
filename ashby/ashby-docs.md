# How to load Ashby data in Python using dlt

**Build a Ashby-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def ashby_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developers.ashbyhq.com",
            "auth": {
                "type": "basic_auth",
                "username": access_token,
                "password": ''
            },
        },
        "resources": [
            "/jobPosting.list",
            "/jobPosting.info",
            "/applicationForm.submit"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='ashby_pipeline',
        destination='duckdb',
        dataset_name='ashby_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(ashby_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from ashby’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Job Postings: List, retrieve, and manage job postings.
- Candidate Management: Submit applications, update candidate assessments, and manage candidate data.
- Hiring Process: Read and write access to hiring process metadata, change application stages.
- Organization Management: Read and write operations on organization data, including adding team members and changing sources.
- Webhooks: Manage webhooks for asynchronous event notifications.

You can combine these endpoints to build pipelines that extract structured content from Ashby workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Ashby support.
    ```shell
    dlt init dlthub:ashby duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Ashby API as specified in @ashby-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in ashby_pipeline.py and name the pipeline ashby_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python ashby_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@ashby-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is managed via Basic Auth with an API key used as the username. A refresh token flow is also mentioned for maintaining session validity.
    
    To get appropriate API keys, please visit the original source at https://developers.ashbyhq.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python ashby_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline ashby load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset ashby_data
    The duckdb destination used duckdb:/ashby.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline ashby_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("ashby_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Webhooks must be manually enabled after ping failures. Specific endpoints require permissions like candidatesRead or jobsWrite. Basic Auth with API key usage is not recommended for browser use due to security concerns. CORS is not enabled, so requests should be proxied through a backend service.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)