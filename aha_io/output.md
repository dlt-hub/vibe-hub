In this guide, we'll set up a complete Aha! data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def aha_roadmaps_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://<yourcompany>.aha.io/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/privacy/personal_information",
            "/privacy/subprocessors",
            "/api/issues"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='aha_roadmaps_pipeline',
        destination='duckdb',
        dataset_name='aha_roadmaps_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(aha_roadmaps_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from aha_roadmaps’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Privacy Management: Manage and access personal data and subprocessor information.
- Issue Tracking: Retrieve issues relating to product development.
- User Stories: Access user stories for agile development tracking.
- Product Development: Access steps and frameworks associated with product development processes.

You can combine these endpoints to build pipelines that extract structured content from Aha! workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@aha_roadmaps-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Aha! support.
    ```shell
    dlt init dlthub:aha_roadmaps duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@aha_roadmaps-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Aha! API, as specified in @aha_roadmaps-docs.yaml 
    Start with endpoints "/privacy/personal_information" and "/privacy/subprocessors" and skip incremental loading for now. 
    Place the code in aha_roadmaps_pipeline.py and name the pipeline aha_roadmaps_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python aha_roadmaps_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via OAuth2, using an authorization code flow. A client ID, client secret, and token URL are required. Tokens must be included in the header for requests.
    
    To get appropriate API keys, please visit the original source at https://www.aha.io/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python aha_roadmaps_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline aha_roadmaps load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset aha_roadmaps_data
    The duckdb destination used duckdb:/aha_roadmaps.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline aha_roadmaps_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("aha_roadmaps_pipeline").dataset()
   # get "/privacy/personal_information" table as Pandas frame
   data."/privacy/personal_information".df().head()
    ```

## Running into errors?

Before using the API, an external application must be registered with Aha!. The user must be a valid Aha! user and a workspace owner in Aha! Roadmaps. Integration with Azure DevOps requires appropriate permissions. A high-speed Internet connection is necessary for proper API function. Aha! adheres to strict privacy policies, especially concerning California users.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)