# How to load Testing Partner data in Python using dlt

**Build a Testing Partner-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def testing_partner_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://www.testing-partner.com",
            "auth": {
                "type": "basic",
                "token": access_token,
            },
        },
        "resources": [
            "/api/test_status",
            "/api/list_tests",
            "/api/send_test"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='testing_partner_pipeline',
        destination='duckdb',
        dataset_name='testing_partner_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(testing_partner_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from testing_partner’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Test Management: Endpoints to manage test status, send tests, and list tests.
- Test Completion: Endpoint to mark tests as completed.

You can combine these endpoints to build pipelines that extract structured content from Testing Partner workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Testing Partner support.
    ```shell
    dlt init dlthub:testing_partner duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Testing Partner API as specified in @testing_partner-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in testing_partner_pipeline.py and name the pipeline testing_partner_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python testing_partner_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@testing_partner-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Uses Basic Authentication with API key. The API key must be Base64 encoded and appended with a colon before adding to the Authorization header. The key length should be less than 171 characters.
    
    To get appropriate API keys, please visit the original source at https://developers.greenhouse.io.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python testing_partner_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline testing_partner load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset testing_partner_data
    The duckdb destination used duckdb:/testing_partner.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline testing_partner_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("testing_partner_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Ensure all API calls are made over HTTPS due to the use of Basic Authentication for security reasons. Always check that the API key is correctly Base64 encoded and its length is under 171 characters to avoid authentication errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)