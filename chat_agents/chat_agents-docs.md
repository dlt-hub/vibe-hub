In this guide, we'll set up a complete Mistral data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def mistral_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.mistral.ai/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "moderations",
            "chat/moderations",
            "classification"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='mistral_pipeline',
        destination='duckdb',
        dataset_name='mistral_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(mistral_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from mistral’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Content Moderation: Provides endpoints for moderating text and chat conversations.
- Text Analysis: Offers classification, summarization, and personalization services.
- Conversation Management: Handles retrieval and management of conversations and their details.
- Payment Information: Retrieves payment statuses and dates.
- AI Model Interactions: Includes endpoints for interacting with AI models, such as generating embeddings or completions.

You can combine these endpoints to build pipelines that extract structured content from Mistral workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@mistral-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Mistral support.
    ```shell
    dlt init dlthub:mistral duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@mistral-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Mistral API, as specified in @mistral-docs.yaml 
    Start with endpoints "moderations" and "chat/moderations" and skip incremental loading for now. 
    Place the code in mistral_pipeline.py and name the pipeline mistral_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python mistral_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is handled via OAuth2 with a refresh token flow. Tokens are obtained from a specific token URL and require client credentials and a refresh token. The token must be included in the header for API requests.
    
    To get appropriate API keys, please visit the original source at https://www.mistral.ai/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python mistral_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline mistral load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset mistral_data
    The duckdb destination used duckdb:/mistral.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline mistral_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("mistral_pipeline").dataset()
   # get "moderations" table as Pandas frame
   data."moderations".df().head()
    ```

## Running into errors?

There are several caveats to consider: the maximum file size limit is 10Mb, the maximum number of images per request is 8, a maximum of 1 million pending requests per workspace is allowed, and custom policies may need recalibration. Additionally, there are specific requirements and limits on API usage to prevent errors such as request limits or unauthorized access.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)