In this guide, we'll set up a complete PhoneBurner data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def phone_burner_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://www.phoneburner.com/rest/1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "members",
            "contacts",
            "voicemails"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='phone_burner_pipeline',
        destination='duckdb',
        dataset_name='phone_burner_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(phone_burner_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from phone_burner’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- OAuth Endpoints: Used for authentication processes, including access and refresh token management.
- Member Management: Endpoints for managing member data.
- Contact Management: For creating, updating, deleting, and retrieving contact details.
- Voicemail Management: For accessing and managing voicemail settings.
- Tag Management: To update tags associated with certain records.

You can combine these endpoints to build pipelines that extract structured content from PhoneBurner workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@phone_burner-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with PhoneBurner support.
    ```shell
    dlt init dlthub:phone_burner duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@phone_burner-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for PhoneBurner API, as specified in @phone_burner-docs.yaml 
    Start with endpoints "members" and "contacts" and skip incremental loading for now. 
    Place the code in phone_burner_pipeline.py and name the pipeline phone_burner_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python phone_burner_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    PhoneBurner uses OAuth for authentication. The client must obtain both an access token and a refresh token using the client credentials. The access token is used in the Authorization header for making API requests to protected resources.
    
    To get appropriate API keys, please visit the original source at https://www.phoneburner.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python phone_burner_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline phone_burner load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset phone_burner_data
    The duckdb destination used duckdb:/phone_burner.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline phone_burner_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("phone_burner_pipeline").dataset()
   # get "members" table as Pandas frame
   data."members".df().head()
    ```

## Running into errors?

The client_secret is vital and should be securely stored as it's not verified in the current OAuth2 client version being used. Refresh tokens have a longer lifespan than access tokens but do expire. All interactions with the API must comply with applicable laws and regulations. Note that the default sorting order is DESC and the default page size for GET requests is 25. Also, be aware of various common HTTP errors such as unauthorized access, bad requests, and unavailable services.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)