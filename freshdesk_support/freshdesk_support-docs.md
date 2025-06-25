In this guide, we'll set up a complete Freshdesk data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def freshdesk_support_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your_domain.freshdesk.com/api/v2/",
            "auth": {
                "type": "apikey",
                "location": "header",
                "header_name": "Authorization"
            },
        },
        "resources": [
            "agents",
            "business_hours",
            "canned_responses"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='freshdesk_support_pipeline',
        destination='duckdb',
        dataset_name='freshdesk_support_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(freshdesk_support_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from freshdesk_support’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Agents: Manage and retrieve information about agents.
- Business Hours: Retrieve information about business hours set in Freshdesk.
- Canned Responses: Access and manage predefined responses for common queries.
- Companies: Manage company records associated with various tickets and contacts.
- Contacts: Handle creation and management of customer contact records.
- Conversations: Access conversation threads in tickets.
- Discussions: Manage and retrieve information on forums, topics, categories, and comments.

You can combine these endpoints to build pipelines that extract structured content from Freshdesk workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@freshdesk_support-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Freshdesk support.
    ```shell
    dlt init dlthub:freshdesk_support duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Freshdesk API, as specified in @freshdesk_support-docs.yaml 
    Start with endpoints "agents" and "business_hours" and skip incremental loading for now. 
    Place the code in freshdesk_support_pipeline.py and name the pipeline freshdesk_support_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python freshdesk_support_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done via API key. The key needs to be included in the header of each API request.
    
    To get appropriate API keys, please visit the original source at https://www.freshdesk.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python freshdesk_support_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline freshdesk_support load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset freshdesk_support_data
    The duckdb destination used duckdb:/freshdesk_support.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline freshdesk_support_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("freshdesk_support_pipeline").dataset()
   # get agents table as Pandas frame
   data.agents.df().head()
    ```

## Running into errors?

Freshdesk API has a rate limit of 50 requests per minute per app per account. It's crucial to handle these limits in your application to avoid service interruption. Additionally, for data requests spanning more than 30 days, use a start date parameter to manage data volume.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)