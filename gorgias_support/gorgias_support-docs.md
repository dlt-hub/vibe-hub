In this guide, we'll set up a complete Gorgias data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def gorgias_support_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://YOUR_DOMAIN.gorgias.com/",
            "auth": {
                "type": "basic",
                "username": access_username,
                "password": access_password,
            },
        },
        "resources": [
            "accounts",
            "customers",
            "custom_fields"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='gorgias_support_pipeline',
        destination='duckdb',
        dataset_name='gorgias_support_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(gorgias_support_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from gorgias_support’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Accounts: Retrieve information about the account.
- Customers: Access and retrieve customer data.
- Custom Fields: Manage and retrieve custom field data.
- Events: View events associated with various services.
- Integrations: Access details on integrated services.
- Jobs: Information about background jobs.
- Macros: Retrieve and manage macros.
- Views: Manage how certain sets of data are presented.
- Rules: Access and manage automation rules.
- Satisfaction Surveys: Retrieve data from customer satisfaction surveys.
- Tags: Manage and retrieve tags.
- Teams: Access team information.
- Tickets: Manage support tickets and their data.
- Messages: Access message history and details.
- Users: Manage user information and roles.
- Views Items: Detailed information on specific views.

You can combine these endpoints to build pipelines that extract structured content from Gorgias workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@gorgias_support-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Gorgias support.
    ```shell
    dlt init dlthub:gorgias_support duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Gorgias API, as specified in @gorgias_support-docs.yaml 
    Start with endpoints "accounts" and "customers" and skip incremental loading for now. 
    Place the code in gorgias_support_pipeline.py and name the pipeline gorgias_support_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python gorgias_support_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses HTTP basic authentication, which requires a username and password.
    
    To get appropriate API keys, please visit the original source at https://www.gorgias.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python gorgias_support_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline gorgias_support load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset gorgias_support_data
    The duckdb destination used duckdb:/gorgias_support.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline gorgias_support_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("gorgias_support_pipeline").dataset()
   # get accounts table as Pandas frame
   data.accounts.df().head()
    ```

## Running into errors?

Ensure that the domain name in the base URL is correctly replaced with your specific Gorgias domain to make successful API calls. Errors such as 401 Unauthorized can occur if username and password are not correct, and 404 Not Found may indicate issues with endpoint paths or parameters.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)