In this guide, we'll set up a complete Merge ATS data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def merge_ats_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.merge.dev/api/ats/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "account-details",
            "activities",
            "applications"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='merge_ats_pipeline',
        destination='duckdb',
        dataset_name='merge_ats_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(merge_ats_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from merge_ats’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Account Details: Retrieve details about the account.
- Activities: Access activity records related to applicants.
- Applications: Fetch information about applications submitted.
- Attachments: Get attachments associated with applications.
- Candidates: Access candidate information.
- Departments: Retrieve information about departments within the organization.
- EEOCs: Access Equal Employment Opportunity Commission related records.
- Interviews: Retrieve scheduled interviews details.
- Job Interview Stages: Access details of different stages in the job interview process.
- Jobs: Fetch job postings and related information.
- Offers: Retrieve details about job offers extended.
- Offices: Get information about company offices.
- Sync Status: Access the status of data synchronization.
- Users: Retrieve user data associated with the account.

You can combine these endpoints to build pipelines that extract structured content from Merge ATS workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@merge_ats-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Merge ATS support.
    ```shell
    dlt init dlthub:merge_ats duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Merge ATS API, as specified in @merge_ats-docs.yaml 
    Start with endpoints "account-details" and "activities" and skip incremental loading for now. 
    Place the code in merge_ats_pipeline.py and name the pipeline merge_ats_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python merge_ats_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done via API Key. The key should be included in the headers of each request. Specifically, the API key needs to be sent as a bearer token in the 'Authorization' header.
    
    To get appropriate API keys, please visit the original source at https://www.merge.dev/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python merge_ats_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline merge_ats load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset merge_ats_data
    The duckdb destination used duckdb:/merge_ats.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline merge_ats_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("merge_ats_pipeline").dataset()
   # get account-details table as Pandas frame
   data.account-details.df().head()
    ```

## Running into errors?

Authentication requires both account_token and api_token. Only full refresh sync is supported, meaning that incremental updates cannot be captured. Ensure the validity of the API and account tokens to avoid 401 Unauthorized errors. Also, verify the correct paths and resource availability to avoid 404 Not Found errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)