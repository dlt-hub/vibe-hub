In this guide, we'll set up a complete Xola data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def xola_migrations_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://sandbox.xola.com/api/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "user_preferences",
            "affiliates",
            "coupons"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='xola_migrations_pipeline',
        destination='duckdb',
        dataset_name='xola_migrations_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(xola_migrations_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from xola_migrations’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- User Preferences: Manage user settings and preferences.
- Affiliate Management: Handle operations related to affiliates, including payments and details.
- Demographics: Retrieve demographic information related to users or experiences.
- Order Adjustments: Modify and manage orders, such as changing quantity or checking in guests.
- Experience Scheduling: Manage schedules and availability for experiences.
- Coupons and Discounts: Operations related to managing coupons, including creation and application behavior.

You can combine these endpoints to build pipelines that extract structured content from Xola workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@xola_migrations-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Xola support.
    ```shell
    dlt init dlthub:xola_migrations duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@xola_migrations-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Xola API, as specified in @xola_migrations-docs.yaml 
    Start with endpoints "user_preferences" and "affiliates" and skip incremental loading for now. 
    Place the code in xola_migrations_pipeline.py and name the pipeline xola_migrations_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python xola_migrations_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Xola utilizes OAuth2 authentication. Tokens are used in authorization headers. There are specific roles and permissions required for different actions, ensuring secure and role-appropriate access to resources.
    
    To get appropriate API keys, please visit the original source at https://www.xola.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python xola_migrations_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline xola_migrations load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset xola_migrations_data
    The duckdb destination used duckdb:/xola_migrations.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline xola_migrations_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("xola_migrations_pipeline").dataset()
   # get "user_preferences" table as Pandas frame
   data."user_preferences".df().head()
    ```

## Running into errors?

API usage involves understanding specific roles like ROLE_CURATOR or ROLE_SELLER_ADMIN for certain actions. Errors must be handled properly, especially those related to resource conflicts or unauthorized access. It's crucial to develop and test in a sandbox environment before production deployment.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)