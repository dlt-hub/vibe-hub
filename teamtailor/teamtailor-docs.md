In this guide, we'll set up a complete Teamtailor data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def teamtailor_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.teamtailor.com/",
            "auth": {
                "type": "apikey",
                "header": {
                    "name": "X-Api-Key",
                    "value": access_token
                }
},
        },
        "resources": [
            "candidates",
            "custom-fields",
            "departments"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='teamtailor_pipeline',
        destination='duckdb',
        dataset_name='teamtailor_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(teamtailor_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from teamtailor’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Candidates: Manage and retrieve candidate information.
- Custom Fields: Access and manage customizable fields for various resources.
- Departments: Interact with department-related data.
- Jobs: Manage job postings and related data.
- Job Applications: Handle applications to job postings.
- Job Offers: Manage job offers extended to candidates.
- Locations: Manage and access location information for the organization.
- Users: Access user profiles and data.
- Todos: Manage tasks and to-dos for users.
- Teams: Manage team information and groupings within the organization.
- Team Memberships: Handle the associations between users and teams.
- Stages: Manage or retrieve stages in recruitment processes.
- Roles: Access role definitions within the organization.
- Regions: Manage geographical region information.
- Referrals: Handle referral information and status.
- Questions: Manage questions for applications or screenings.
- Notes: Store and retrieve notes on various entities.
- NPS Responses: Access Net Promoter Score responses from surveys.

You can combine these endpoints to build pipelines that extract structured content from Teamtailor workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@teamtailor-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Teamtailor support.
    ```shell
    dlt init dlthub:teamtailor duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Teamtailor API, as specified in @teamtailor-docs.yaml 
    Start with endpoints "candidates" and "custom-fields" and skip incremental loading for now. 
    Place the code in teamtailor_pipeline.py and name the pipeline teamtailor_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python teamtailor_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication uses an API key which should be included in the header of each request.
    
    To get appropriate API keys, please visit the original source at https://www.teamtailor.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python teamtailor_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline teamtailor load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset teamtailor_data
    The duckdb destination used duckdb:/teamtailor.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline teamtailor_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("teamtailor_pipeline").dataset()
   # get candidates table as Pandas frame
   data.candidates.df().head()
    ```

## Running into errors?

Ensure that add-ons are installed to use the nps-responses and job-offers endpoints. Also, be aware of common errors like '401 Unauthorized' which suggests issues with the API key validity or permissions, and '404 Not Found' which indicates potential problems with the endpoint path or resource availability.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)