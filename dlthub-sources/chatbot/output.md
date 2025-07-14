In this guide, we'll set up a complete Chatbot data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def chatbot_com_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.chatbot.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "entities",
            "stories",
            "chats"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='chatbot_com_pipeline',
        destination='duckdb',
        dataset_name='chatbot_com_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(chatbot_com_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from chatbot_com’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Entity Management: Endpoints for managing entities such as creating, retrieving, updating, and deleting entities.
- Connection Management: Endpoints to manage connections including listing, creating, and modifying connections.
- Story Management: Manage chatbot stories, allowing for creation, retrieval, updates, and deletions.
- Chat & User Data: Endpoints for retrieving chat data and user information, useful for monitoring and managing user interactions.

You can combine these endpoints to build pipelines that extract structured content from Chatbot workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@chatbot_com-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Chatbot support.
    ```shell
    dlt init dlthub:chatbot_com duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@chatbot_com-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Chatbot API, as specified in @chatbot_com-docs.yaml 
    Start with endpoints "entities" and "stories" and skip incremental loading for now. 
    Place the code in chatbot_com_pipeline.py and name the pipeline chatbot_com_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python chatbot_com_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The API uses OAuth 2.0 for authentication, specifically with Bearer tokens included in the Authorization header.
    
    To get appropriate API keys, please visit the original source at https://www.chatbot.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python chatbot_com_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline chatbot_com load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset chatbot_com_data
    The duckdb destination used duckdb:/chatbot_com.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline chatbot_com_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("chatbot_com_pipeline").dataset()
   # get "entities" table as Pandas frame
   data."entities".df().head()
    ```

## Running into errors?

It is crucial to handle authorization tokens securely to avoid unauthorized access. Ensure correct API usage by adhering to the specified request formats and handling potential errors such as 400 (Bad Request), 401 (Unauthorized), 404 (Not Found), and 500 (Internal Server Error) appropriately.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)