In this guide, we'll set up a complete H2O data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def h2o_migrations_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://s3.amazonaws.com/h2o-public-test-data/v/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            n,,f1,,ls
            ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='h2o_migrations_pipeline',
        destination='duckdb',
        dataset_name='h2o_migrations_data', 
    )
    # Load the data
    load_info = pipeline.run(h2o_migrations_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from h2o_migrations’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- n: Returns a summary of the dataset.
- f1: Retrieves first feature/column in the dataset.
- ls: Lists available datasets.
- f2: Retrieves second feature/column in the dataset.
- f1: Retrieves first feature/column in the dataset.
- f2: Retrieves second feature/column in the dataset.
- ate: Retrieves automatic threshold evaluation.
- mcc: Computes Matthews correlation coefficient.
- atc: Computes area under the curve.
- att: Computes average treatment effect.
- auc: Computes area under the curve.
- glm: Generalized linear model functionalities.
- grid: Manages grid search functionalities.
- Grep: Searches for a pattern in datasets.
- F0.5: Computes F0.5 score.
- auuc: Computes area under the uplift curve.
- f0.5: Computes F0.5 score.
- init: Initializes model parameters.
- mojo: Handles MOJO (Model Object, Optimized) functionalities.
- ping: Checks if the server is responsive.

You will then debug the H2O pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with H2O support.
    ```shell
    dlt init dlthub:h2o_migrations duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for H2O API, as specified in @h2o_migrations-docs.yaml 
    Start with endpoints n and  and skip incremental loading for now. 
    Place the code in h2o_migrations_pipeline.py and name the pipeline h2o_migrations_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python h2o_migrations_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    Authentication is implemented using Basic Auth. Ensure proper API key management and follow security guidelines when securing your installation. Uses OAuth2 with refresh token, which requires setting up a connected app in H2O.
    
    To get the appropriate API keys, please visit the original source at https://www.h2o.ai/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python h2o_migrations_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline h2o_migrations load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset h2o_migrations_data
    The duckdb destination used duckdb:/h2o_migrations.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline h2o_migrations_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("h2o_migrations_pipeline").dataset()
   # get  table as Pandas frame
   data..df().head()
    ```

## Running into errors?

H2O does not provide in-memory data encryption, which might expose data to disk in unencrypted form. Ensure that H2O security settings are correctly configured to prevent unauthorized access. Older Linux kernel versions can cause issues when running Docker. H2O nodes should be treated as stateful in Kubernetes environments.

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
