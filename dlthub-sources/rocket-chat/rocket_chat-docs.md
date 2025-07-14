# How to load Rocket.Chat data in Python using dlt

**Build a Rocket.Chat-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def rocket_chat_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.rocket.chat",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/api/v1/files",
            "/api/v1/messages",
            "/api/v1/users"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='rocket_chat_pipeline',
        destination='duckdb',
        dataset_name='rocket_chat_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(rocket_chat_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from rocket_chat’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Files: Allows for uploading files.
- Messages: Enables retrieval of messages.
- Users: Manages user information and operations.
- Authentication: Provides endpoints for user authentication and server addition.
- Rooms: Manages direct room links, invitations, and conference calls.
- Notifications: Handles push notifications and websocket connections for real-time communication.
- Integration: Offers iframe integration and retrieval of default server configurations.

You can combine these endpoints to build pipelines that extract structured content from Rocket.Chat workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Rocket.Chat support.
    ```shell
    dlt init dlthub:rocket_chat duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Rocket.Chat API as specified in @rocket_chat-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in rocket_chat_pipeline.py and name the pipeline rocket_chat_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python rocket_chat_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@rocket_chat-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Supports OAuth2 and other authentication protocols like SAML and LDAP. Authentication is typically done via headers with tokens obtained from a specified token URL (OAuth2 flow). Personal Access Tokens are also supported, which do not expire and bypass two-factor authentication (2FA).
    
    To get appropriate API keys, please visit the original source at https://developer.rocket.chatRocket.Chat.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python rocket_chat_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline rocket_chat load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset rocket_chat_data
    The duckdb destination used duckdb:/rocket_chat.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline rocket_chat_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("rocket_chat_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

It is crucial to use HTTPS for all authentication requests to protect user credentials. Regularly update and renew OAuth tokens to prevent unauthorized access. Be mindful of API rate limits to avoid exceeding request limits, which could result in temporary blocking of API access.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)