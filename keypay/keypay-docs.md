In this guide, we'll set up a complete Employment Hero Payroll data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def employment_hero_payroll_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.example.com/api/v2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "leaveallowancetemplate",
            "employee",
            "leaveallowances"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='employment_hero_payroll_pipeline',
        destination='duckdb',
        dataset_name='employment_hero_payroll_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(employment_hero_payroll_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from employment_hero_payroll’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Leave Management: Includes endpoints for creating, updating, and deleting leave requests and templates. 
- Employee Management: Involves managing employee details, banking info, and access rights. 
- Payroll Management: Allows manipulation of payroll settings, earnings lines, and tax adjustments. 
- Single Sign-On (SSO): Endpoints for generating SSO URLs for employees and businesses. 
- Reporting: Provides endpoints for generating various reports like active employee reports.

You can combine these endpoints to build pipelines that extract structured content from Employment Hero Payroll workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@employment_hero_payroll-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Employment Hero Payroll support.
    ```shell
    dlt init dlthub:employment_hero_payroll duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@employment_hero_payroll-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Employment Hero Payroll API, as specified in @employment_hero_payroll-docs.yaml 
    Start with endpoints "leaveallowancetemplate" and "employee" and skip incremental loading for now. 
    Place the code in employment_hero_payroll_pipeline.py and name the pipeline employment_hero_payroll_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python employment_hero_payroll_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done via OAuth2. An access token is obtained through a token URL and used for subsequent requests. The token expires every 24 hours, requiring refresh using a long-lived refresh token valid for 28 days.
    
    To get appropriate API keys, please visit the original source at https://employmenthero.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python employment_hero_payroll_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline employment_hero_payroll load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset employment_hero_payroll_data
    The duckdb destination used duckdb:/employment_hero_payroll.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline employment_hero_payroll_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("employment_hero_payroll_pipeline").dataset()
   # get "leaveallowancetemplate" table as Pandas frame
   data."leaveallowancetemplate".df().head()
    ```

## Running into errors?

API is limited to 5 requests per second per API key per IP. Tokens expire every 24 hours. Refresh token handling is crucial. The API does not support the $expand parameter and paginates employee lists to 100 entries per request.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)