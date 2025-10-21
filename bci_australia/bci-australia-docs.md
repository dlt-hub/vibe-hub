In this guide, we'll set up a complete BCI Australia data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def bci_australia_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "http://api.bciaustralia.com/rest",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "companies(.json", "projects(.json"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='bci_australia_pipeline',
        destination='duckdb',
        dataset_name='bci_australia_data', 
    )
    # Load the data
    load_info = pipeline.run(bci_australia_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from bci_australia’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Projects**: Endpoints related to project information including project details and categorizations.
- **Companies**: Endpoints for accessing company-related data.
- **Project Categories**: Endpoints that provide information about various categories of projects.
- **Project Subcategories**: Endpoints for retrieving subcategories of projects.
- **Project Stages**: Endpoints that detail the different stages a project can go through.
- **Project Owner Types**: Endpoints related to the types of owners for projects.
- **Project Statuses**: Endpoints that provide information on the statuses of projects.
- **Project Tender Types**: Endpoints related to the types of tenders for projects.
- **Role Groups**: Endpoints that categorize different role groups within the system.
- **Roles**: Endpoints for accessing various roles assigned in the system.
- **Countries**: Endpoints that provide a list of countries.
- **States**: Endpoints for accessing state-related information.
- **Green Stars**: Endpoints related to green star ratings for projects, including specific green star details.
- **Project Stages Statuses**: Endpoints that provide statuses related to project stages.
- **Regions**: Endpoints for retrieving regional information.
- **Green Building Ratings**: Endpoints related to ratings for green building projects.
- **Lead Manager IDs**: Endpoints for accessing lead manager identifiers and related data.

You will then debug the BCI Australia pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - We suggest using a model like Claude 3.7 Sonnet or better
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started!

1. ⚙️ **Set up `dlt` Workspace**
    
    Install dlt with duckdb support:
    ```shell
    pip install "dlt[workspace]"
    ```

    Initialize a dlt pipeline with BCI Australia support.
    ```shell
    dlt init dlthub:bci_australia duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for BCI Australia API, as specified in @bci_australia-docs.yaml 
    Start with endpoints companies(.json and projects(.json and skip incremental loading for now. 
    Place the code in bci_australia_pipeline.py and name the pipeline bci_australia_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python bci_australia_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate, you need a client ID which can be obtained by using 'clientID=bciapiuser' in your requests, and a password which is provided as 'password=dummypassword'; you will also need to parse the response to extract the token, which is required for subsequent requests.
    
    To get the appropriate API keys, please visit the original source at https://api.bciaustralia.com/doc/usage-steps.htm.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python bci_australia_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline bci_australia load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset bci_australia_data
    The duckdb destination used duckdb:/bci_australia.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline bci_australia_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("bci_australia_pipeline").dataset()
   # get "companies(.json" table as Pandas frame
   data."companies(.json".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
