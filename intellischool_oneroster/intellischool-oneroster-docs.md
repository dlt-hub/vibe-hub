In this guide, we'll set up a complete Intellischool data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def intellischool_oneroster_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.intellischool.net/ims/oneroster/v1p1/academicSessions",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "77c940c5-ab1c-4afd-bfcf-99402eb0ff40", "fdfd80be-1f35-4d09-88fb-6ddbe380278b"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='intellischool_oneroster_pipeline',
        destination='duckdb',
        dataset_name='intellischool_oneroster_data', 
    )
    # Load the data
    load_info = pipeline.run(intellischool_oneroster_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from intellischool_oneroster’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Users**: Endpoints related to user information, including retrieving, creating, and managing users (e.g., `/ims/oneroster/v1p1/users`, `/ims/oneroster/v1p1/users/{id}`).
- **Courses**: Endpoints for managing course information, including listing and retrieving specific courses (e.g., `/ims/oneroster/v1p1/courses`, `/ims/oneroster/v1p1/courses/{id}`).
- **Terms**: Endpoints associated with academic terms, allowing for retrieval of term details (e.g., `/ims/oneroster/v1p1/terms/{id}`).
- **Schools**: Endpoints for managing school information, including listing and retrieving specific schools (e.g., `/ims/oneroster/v1p1/schools`, `/ims/oneroster/v1p1/schools/{id}`).
- **Students**: Endpoints related to student information, including retrieving, creating, and managing students (e.g., `/ims/oneroster/v1p1/students`, `/ims/oneroster/v1p1/students/{id}`).
- **Enrollments**: Endpoints for managing course enrollments for students (e.g., `/ims/oneroster/v1p1/enrollments`).
- **Classes**: Endpoints concerning class information, including listing and retrieving specific classes (e.g., `/ims/oneroster/v1p1/classes`, `/ims/oneroster/v1p1/classes/{id}`).
- **Teachers**: Endpoints related to teacher information, including retrieving and managing teachers (e.g., `/ims/oneroster/v1p1/teachers`, `/ims/oneroster/v1p1/teachers/{id}`).
- **Academic Sessions**: Endpoints for managing academic sessions, including retrieving session details (e.g., `/ims/oneroster/v1p1/academicSessions`, `/ims/oneroster/v1p1/academicSessions/{id}`).
- **Grading Periods**: Endpoints related to managing grading periods within the academic calendar (e.g., `/ims/oneroster/v1p1/gradingPeriods`).
- **Organizations**: Endpoints that relate to managing organizations within the educational context (e.g., `/ims/oneroster/v1p1/orgs`).

You will then debug the Intellischool pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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
    pip install dlt[workspace]
    ```

    Initialize a dlt pipeline with Intellischool support.
    ```shell
    dlt init dlthub:intellischool_oneroster duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Intellischool API, as specified in @intellischool_oneroster-docs.yaml 
    Start with endpoints 77c940c5-ab1c-4afd-bfcf-99402eb0ff40 and fdfd80be-1f35-4d09-88fb-6ddbe380278b and skip incremental loading for now. 
    Place the code in intellischool_oneroster_pipeline.py and name the pipeline intellischool_oneroster_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python intellischool_oneroster_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To access the Intellischool OneRoster API, you must include an authorization token in the header as "Authorization: Bearer {token}", where {token} is obtained from your authentication process, and you need to ensure that your auth token has at least one of the required scopes enabled.
    
    To get the appropriate API keys, please visit the original source at https://docs.intellischool.co/api/oneroster-1p1.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python intellischool_oneroster_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline intellischool_oneroster load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset intellischool_oneroster_data
    The duckdb destination used duckdb:/intellischool_oneroster.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline intellischool_oneroster_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("intellischool_oneroster_pipeline").dataset()
   # get "77c940c5-ab1c-4afd-bfcf-99402eb0ff40" table as Pandas frame
   data."77c940c5-ab1c-4afd-bfcf-99402eb0ff40".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)