In this guide, we'll set up a complete API Name data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def api_name_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.api-name.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "survey",
            "domain_list",
            "contactlist"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='api_name_pipeline',
        destination='duckdb',
        dataset_name='api_name_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(api_name_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from api_name’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Survey Management: Handles operations related to surveys such as creating, retrieving, and managing surveys.
- Domain Management: Manages domain-related tasks such as listing, creating, updating, and deleting domains.
- Contact List Management: Deals with managing contact lists, including creating, updating, retrieving, and deleting contact lists and their details.

You can combine these endpoints to build pipelines that extract structured content from API Name workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@api_name-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with API Name support.
    ```shell
    dlt init dlthub:api_name duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@api_name-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for API Name API, as specified in @api_name-docs.yaml 
    Start with endpoints "survey" and "domain_list" and skip incremental loading for now. 
    Place the code in api_name_pipeline.py and name the pipeline api_name_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python api_name_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The API uses OAuth2 with a refresh token for authentication. It requires setup of a connected app in the API platform. The authentication process involves obtaining an access token from a token URL using a client ID, client secret, and refresh token. The access token is then used in subsequent API calls by including it in the Authorization header.
    
    To get appropriate API keys, please visit the original source at https://www.api-name.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python api_name_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline api_name load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset api_name_data
    The duckdb destination used duckdb:/api_name.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline api_name_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("api_name_pipeline").dataset()
   # get "survey" table as Pandas frame
   data."survey".df().head()
    ```

## Running into errors?

API access is exclusive to enterprise customers. There are strict rate limits, with a maximum of 240 requests per minute for Professional and Full Access accounts. Additionally, API responses can take up to 30 seconds, with timeouts occurring after 30 seconds resulting in a 500 error. Subsequent calls post-timeout will result in a 400 error for 60 seconds. Repeated identical GET requests should be spaced at least 60 seconds apart and results should be cached to avoid hitting rate limits and to handle potential caching of identical GET requests by the API.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)