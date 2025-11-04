In this guide, we'll set up a complete Kubeflow Pipelines data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def kubeflow_pipelines_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://ghcr.io/kubeflow/kubeflow/notebook-servers",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "jupyter-pytorch-gaudi-full", "rstudio"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='kubeflow_pipelines_pipeline',
        destination='duckdb',
        dataset_name='kubeflow_pipelines_data', 
    )
    # Load the data
    load_info = pipeline.run(kubeflow_pipelines_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from kubeflow_pipelines’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Notebook Servers**: Endpoints for various types of notebook environments, primarily for data science and machine learning tasks, including:
  - **RStudio with Tidyverse**: A notebook server configured for R programming with Tidyverse libraries.
  - **Jupyter with PyTorch (Gaudi)**: A server set up for PyTorch development on Gaudi hardware.
  - **Jupyter with TensorFlow (CUDA)**: A Jupyter server optimized for TensorFlow using CUDA for GPU acceleration.
  - **Jupyter**: A basic Jupyter notebook server for general use.
  - **Jupyter with TensorFlow**: A server specifically for TensorFlow development.
  - **Jupyter with PyTorch (CUDA)**: A setup for PyTorch utilizing CUDA for enhanced performance.
  - **Jupyter with Scipy**: A server configured for scientific computing with the SciPy library.
  - **CodeServer for Python**: A code server environment tailored for Python development.
  - **Base Jupyter Server**: A minimal Jupyter server setup without specific configurations.
  - **RStudio**: A notebook server for R programming without additional libraries.

You will then debug the Kubeflow Pipelines pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Kubeflow Pipelines support.
    ```shell
    dlt init dlthub:kubeflow_pipelines duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Kubeflow Pipelines API, as specified in @kubeflow_pipelines-docs.yaml 
    Start with endpoints jupyter-pytorch-gaudi-full and rstudio and skip incremental loading for now. 
    Place the code in kubeflow_pipelines_pipeline.py and name the pipeline kubeflow_pipelines_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python kubeflow_pipelines_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    The snippets do not contain any specific authentication information such as keys, tokens, client IDs, client secrets, or any relevant URLs or headers.
    
    To get the appropriate API keys, please visit the original source at https://www.kubeflow.org/docs/components/pipelines/legacy-v1/reference/api/kubeflow-pipeline-api-spec/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python kubeflow_pipelines_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline kubeflow_pipelines load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset kubeflow_pipelines_data
    The duckdb destination used duckdb:/kubeflow_pipelines.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline kubeflow_pipelines_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("kubeflow_pipelines_pipeline").dataset()
   # get "jupyter-pytorch-gaudi-full" table as Pandas frame
   data."jupyter-pytorch-gaudi-full".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
