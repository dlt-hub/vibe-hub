# How to load Chrome Developer Documentation data in Python using dlt

**Build a Chrome Developer Documentation-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def chrome_dev_docs_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.chrome.com/docs",
            "auth": {
                "type": "apikey",
                "token": access_token,
            },
        },
        "resources": [
            "/translator",
            "/language_detector",
            "/summarizer"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='chrome_dev_docs_pipeline',
        destination='duckdb',
        dataset_name='chrome_dev_docs_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(chrome_dev_docs_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from chrome_dev_docs’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Translation Tools: Includes endpoints for translating text and detecting languages.
- Summarization Tools: Provides endpoints for summarizing text content.
- Writing Assistance: Features endpoints for writing, rewriting, and proofreading text.
- File Storage: Manages file storage and retrieval in browser cache and other storage options like OPFS.

You can combine these endpoints to build pipelines that extract structured content from Chrome Developer Documentation workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Chrome Developer Documentation support.
    ```shell
    dlt init dlthub:chrome_dev_docs duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Chrome Developer Documentation API as specified in @chrome_dev_docs-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in chrome_dev_docs_pipeline.py and name the pipeline chrome_dev_docs_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python chrome_dev_docs_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@chrome_dev_docs-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    The API does not require authentication and is designed for in-browser storage.
    
    To get appropriate API keys, please visit the original source at https://developer.chrome.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python chrome_dev_docs_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline chrome_dev_docs load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset chrome_dev_docs_data
    The duckdb destination used duckdb:/chrome_dev_docs.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline chrome_dev_docs_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("chrome_dev_docs_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

API availability varies by Chrome version and program participation. Some features are experimental and only available in Chrome Canary. The API is integrated directly into the Chrome browser and may have specific hardware and browser version requirements. The use of certain APIs might require significant storage and GPU resources. Third-party cookies phase-out starting Q1 2024 could affect some functionalities.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)