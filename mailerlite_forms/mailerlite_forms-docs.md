In this guide, we'll set up a complete MailerLite data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def mailerlite_forms_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.api.mailerlite.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "forms_popup",
            "forms_promotion"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='mailerlite_forms_pipeline',
        destination='duckdb',
        dataset_name='mailerlite_forms_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(mailerlite_forms_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from mailerlite_forms’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Forms Popup: Endpoint for retrieving information about popup forms.
- Forms Promotion: Endpoint for accessing promotional form settings.

You can combine these endpoints to build pipelines that extract structured content from MailerLite workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@mailerlite_forms-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with MailerLite support.
    ```shell
    dlt init dlthub:mailerlite_forms duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for MailerLite API, as specified in @mailerlite_forms-docs.yaml 
    Start with endpoints "forms_popup" and "forms_promotion" and skip incremental loading for now. 
    Place the code in mailerlite_forms_pipeline.py and name the pipeline mailerlite_forms_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python mailerlite_forms_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses OAuth 2.0 with a refresh token flow. Authentication requires a client ID, client secret, and refresh token. The token is passed in the header under the 'Authorization' key.
    
    To get appropriate API keys, please visit the original source at https://www.mailerlite.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python mailerlite_forms_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline mailerlite_forms load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset mailerlite_forms_data
    The duckdb destination used duckdb:/mailerlite_forms.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline mailerlite_forms_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("mailerlite_forms_pipeline").dataset()
   # get forms_popup table as Pandas frame
   data.forms_popup.df().head()
    ```

## Running into errors?

The forms_popup and forms_promotion streams have recent changes that may affect data handling, specifically with fields 'schedule' and 'double_optin' now using booleans instead of strings. Additionally, there are API call limits, so throttle management and efficient query practices are required. If unauthorized errors occur, OAuth scopes or token expiration should be rechecked.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)