In this guide, we'll set up a complete Wufoo data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def wufoo_forms_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://{subdomain}.wufoo.com/api/v3/",
            "auth": {
                "type": "apikey",
                "token": access_token
            },
        },
        "resources": [
            "forms",
            "form_comments",
            "form_fields"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='wufoo_forms_pipeline',
        destination='duckdb',
        dataset_name='wufoo_forms_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(wufoo_forms_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from wufoo_forms’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Forms: Manage and retrieve form structures and metadata.
- Form Comments: Access and manage comments on forms.
- Form Fields: Handle the fields within a form, including their properties and metadata.
- Form Entries: Work with entries submitted through forms, including retrieval and management.
- Reports: Create and manage reports that summarize form data.
- Report Entries: Access entries associated with specific reports.
- Report Fields: Manage and retrieve fields associated with specific reports.
- Report Widgets: Deal with widgets that are part of the report's layout.
- Users: Retrieve information about users who have access to the Wufoo account and forms.

You can combine these endpoints to build pipelines that extract structured content from Wufoo workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@wufoo_forms-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Wufoo support.
    ```shell
    dlt init dlthub:wufoo_forms duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Wufoo API, as specified in @wufoo_forms-docs.yaml 
    Start with endpoints "forms" and "form_comments" and skip incremental loading for now. 
    Place the code in wufoo_forms_pipeline.py and name the pipeline wufoo_forms_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python wufoo_forms_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is performed using an API key. The key must be included in the header of each request.
    
    To get appropriate API keys, please visit the original source at https://www.wufoo.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python wufoo_forms_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline wufoo_forms load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset wufoo_forms_data
    The duckdb destination used duckdb:/wufoo_forms.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline wufoo_forms_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("wufoo_forms_pipeline").dataset()
   # get forms table as Pandas frame
   data.forms.df().head()
    ```

## Running into errors?

Ensure that the API key is valid and has the necessary permissions to access the desired resources. All requests must include the API key in the header, and users should handle the key securely to prevent unauthorized access.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)