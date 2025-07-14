In this guide, we'll set up a complete Sapling data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def sapling_migration_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://sapling.ai/",
            "auth": {
                "type": "bearer",
                "token": SAPLING_API_KEY,
            },
        },
        "resources": [
            "prepostprocessing",
            "edits",
            "autocomplete"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='sapling_migration_pipeline',
        destination='duckdb',
        dataset_name='sapling_migration_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(sapling_migration_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from sapling_migration’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Preprocessing: Handles tasks before and after processing text inputs.
- Edits: Provides functionality for editing text content.
- Autocomplete: Aids in completing text inputs.
- Rephrase: Offers rephrasing suggestions for given text.
- Summarize: Summarizes larger text inputs.
- Memory & Retrieval: Manages memory and retrieval tasks related to data.
- Scoring & Classification: Assists in scoring and classifying text.
- AI Detector: Detects AI-generated content.
- Benchmarking & Evaluation: Evaluates the performance of models.
- Guardrails: Provides safety checks and balances for AI outputs.
- API Reporting: Reports on the API usage and performance metrics.
- Team Management: Facilitates team-related functions within the API.

You can combine these endpoints to build pipelines that extract structured content from Sapling workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@sapling_migration-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Sapling support.
    ```shell
    dlt init dlthub:sapling_migration duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@sapling_migration-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Sapling API, as specified in @sapling_migration-docs.yaml 
    Start with endpoints "prepostprocessing" and "edits" and skip incremental loading for now. 
    Place the code in sapling_migration_pipeline.py and name the pipeline sapling_migration_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python sapling_migration_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The API utilizes an API key for authentication, which must be included in the request header under the name 'Authorization'.
    
    To get appropriate API keys, please visit the original source at https://www.sapling.ai/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python sapling_migration_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline sapling_migration load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset sapling_migration_data
    The duckdb destination used duckdb:/sapling_migration.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline sapling_migration_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("sapling_migration_pipeline").dataset()
   # get "prepostprocessing" table as Pandas frame
   data."prepostprocessing".df().head()
    ```

## Running into errors?

Users should be aware of potential rate limits associated with their API key subscriptions, which vary by endpoint and can impact their usage. Additionally, the service may require specific security protocols for certain environments. Free trial users have significantly lower character limits compared to subscribed users. If encountering rate limit errors (status code 429), reaching out to support may be necessary.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)