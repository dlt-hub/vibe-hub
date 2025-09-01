In this guide, we'll set up a complete PDF.co data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def pdf_co_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.pdf.co/v1/",
            "auth": {
                "type": "apikey",
                "token": access_token,
            },
        },
        "resources": [
            "xls/convert/to/txt",
            "pdf/convert/from/email",
            "pdf/optimize"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='pdf_co_pipeline',
        destination='duckdb',
        dataset_name='pdf_co_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(pdf_co_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from pdf_co’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Document Conversion: Convert documents from one format to another, such as PDF to TXT or HTML to PDF.
- PDF Information: Retrieve detailed information or metadata about PDF documents, such as fields or optimization options.
- PDF Editing: Includes capabilities like rotating, deleting pages, or extracting attachments from PDF documents.

You can combine these endpoints to build pipelines that extract structured content from PDF.co workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@pdf_co-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with PDF.co support.
    ```shell
    dlt init dlthub:pdf_co duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@pdf_co-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for PDF.co API, as specified in @pdf_co-docs.yaml 
    Start with endpoints "xls/convert/to/txt" and "pdf/convert/from/email" and skip incremental loading for now. 
    Place the code in pdf_co_pipeline.py and name the pipeline pdf_co_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python pdf_co_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done using an API key included in the header of each request. The name of the header field for the API key is 'x-api-key'.
    
    To get appropriate API keys, please visit the original source at https://www.pdf.co/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python pdf_co_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline pdf_co load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset pdf_co_data
    The duckdb destination used duckdb:/pdf_co.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline pdf_co_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("pdf_co_pipeline").dataset()
   # get "xls/convert/to/txt" table as Pandas frame
   data."xls/convert/to/txt".df().head()
    ```

## Running into errors?

Be aware of rate limits and potential access denied errors which suggest using cache to reduce frequency of requests. For large documents or files, it is recommended to use asynchronous mode to avoid timeout errors. Output files are securely stored but expire in 1 hour, making timely retrieval essential.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)