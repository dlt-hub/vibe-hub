In this guide, we'll set up a complete MailChimp data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def mailchimp_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://<dc>.api.mailchimp.com/3.0/",
            "auth": {
                "type": "apikey",
                "token": access_token,
            },
        },
        "resources": [
            "automations",
            "campaigns",
            "lists"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='mailchimp_pipeline',
        destination='duckdb',
        dataset_name='mailchimp_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(mailchimp_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from mailchimp’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Automations: Access and manage your marketing automations.
- Campaigns: Retrieve and manage email campaigns.
- Email Activity: Fetch detailed email activity reports.
- Lists: Manage audience lists and their members.
- Reports: Access detailed reports on your campaigns.
- Segments: Manage audience segments and segment members.

You can combine these endpoints to build pipelines that extract structured content from MailChimp workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@mailchimp-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with MailChimp support.
    ```shell
    dlt init dlthub:mailchimp duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for MailChimp API, as specified in @mailchimp-docs.yaml 
    Start with endpoints "automations" and "campaigns" and skip incremental loading for now. 
    Place the code in mailchimp_pipeline.py and name the pipeline mailchimp_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python mailchimp_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is performed via API key.
    
    To get appropriate API keys, please visit the original source at https://www.mailchimp.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python mailchimp_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline mailchimp load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset mailchimp_data
    The duckdb destination used duckdb:/mailchimp.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline mailchimp_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("mailchimp_pipeline").dataset()
   # get automations table as Pandas frame
   data.automations.df().head()
    ```

## Running into errors?

The API has removed the `._links` field from all streams, which may affect navigation between related data. Empty strings in responses are converted to null, which may require handling in data processing. It's important to handle HTTP status codes like 400, 401, 404, and 429 by checking parameters, verifying API keys, ensuring resources exist, and implementing retry strategies like exponential backoff.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)