In this guide, we'll set up a complete Cinchy data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def cinchy_forms_metadata_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.cinchy.co/",
            "auth": {
                "type": "basic",
                "client_id": access_client_id,
                "client_secret": access_client_secret
            },
        },
        "resources": [
            "comments",
            "forms",
            "jobs"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='cinchy_forms_metadata_pipeline',
        destination='duckdb',
        dataset_name='cinchy_forms_metadata_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(cinchy_forms_metadata_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from cinchy_forms_metadata’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Comments: Retrieve comments from Cinchy.
- Forms: Access and manage form data.
- Form Sections: Handle sections of forms including metadata.
- Form Fields: Manage individual fields within forms.
- Jobs: Submit and manage jobs within Cinchy.
- Secrets Manager: Access and manage secrets.
- System Properties: Retrieve system-wide properties and configurations.
- Network Map: Visualize data network configurations.
- Admin Panel: Access the administration panel functionalities.
- Saved Queries: Execute and manage saved queries within Cinchy.

You can combine these endpoints to build pipelines that extract structured content from Cinchy workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@cinchy_forms_metadata-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Cinchy support.
    ```shell
    dlt init dlthub:cinchy_forms_metadata duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@cinchy_forms_metadata-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Cinchy API, as specified in @cinchy_forms_metadata-docs.yaml 
    Start with endpoints "comments" and "forms" and skip incremental loading for now. 
    Place the code in cinchy_forms_metadata_pipeline.py and name the pipeline cinchy_forms_metadata_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python cinchy_forms_metadata_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Cinchy uses basic authentication with client authentication via client ID and client secret.
    
    To get appropriate API keys, please visit the original source at https://www.cinchy.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python cinchy_forms_metadata_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline cinchy_forms_metadata load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset cinchy_forms_metadata_data
    The duckdb destination used duckdb:/cinchy_forms_metadata.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline cinchy_forms_metadata_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("cinchy_forms_metadata_pipeline").dataset()
   # get "comments" table as Pandas frame
   data."comments".df().head()
    ```

## Running into errors?

Cinchy has several important operational notes such as enabling listeners for real-time syncs, handling table deletions carefully as data remains accessible technically, and using bearer tokens for authentication except for the jobs endpoint which does not support them. It is also important to handle the platform updates and configurations carefully especially when deploying on Kubernetes.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)