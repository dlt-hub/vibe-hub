In this guide, we'll set up a complete Société Générale Berlin Group PSD2 API data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def societe_generale_berlin_group_psd2_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://consumer.finologee.com/consumer-api/oauth2",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "authorize?client_id=9cf005e2-56c3-4a5d-8546-6dc085eef7f5&state=czozMjI6ImRlZjUwMjAwMzFmMWQ3YzdiYzQzNmU4NjJlMGIwZTQxZjg0ZTJhOTdiYTUyZmZhNzI4MjQ1NGNjMWI4NjhlY2UxNDg2NTE3YTVmMzgzMjUwOWIwYWFkYTI5ZWY2MGM5MDAyZGY1Njc0NTE4NThjOGM3NTEyY2I0NDQ5ODExZmNiMjgyMTJkYjFjMmVkZDI4ZWQxMTA3NDhlMTZiMjc1ODg2YjQ1ZDFiZTY0YTNjNTM0ZTg3NzU5NTYzYmI1ZjRmNDczNWJkNTQzN2MyZTc4Y2Y4NWI4MjlmOWM2NmJlNzU5NzE2YjExMGU4ZWY5MGRmNDc2M2Y5ZjYyYjI1YTVmMzg5N2E3MjVmZTg2OTg4MDg3NGNmODBhYzkxYjhhOWNhMzQyOTIwOGMyYzNhN2ZlNGIxYjk1N2Q5MTUzNGQyYjVjOTkiOw,,&redirect_uri=https:", "authorize?client_id=9cf005e2-56c3-4a5d-8546-6dc085eef7f5&state=czozMjI6ImRlZjUwMjAwYWE0MjRmN2Y1ZmMxMTc1MDg2ZDMzN2FhZDI2MDQ3MTZhZGZlMWEyNGE2YjgxYzA4NjY3ZjI0Yzg1Yjk2N2I2Mjg0MzY2ZjU5N2Q3OGQ4NTIxMjRmNTE3NzEwZDEzYTkxYjQyYWZmMDVmODU0NjUyNDQ0MjhiMDgzYzM2MjYzNjEyMDU0N2NiYWZkMzI1YjNmMjIzZmU4YzY3NzA2MmNjOGUyYTdhNDExMmYzN2M5YzFkYmYwZjhmNTNlNTMwMzIxNmFiNTY3YmMzMjYzN2U1NTA4YjIwYjM5MmFmODE3YzhiMzZlOGNmZWMyYzgzNjE3MjgyNWJjOWMzYTA5OGVlMzViMjMwNWYwMzNjYzQ3ZTk4OGFhODQxMWRjZmMyMjkyMjcxYWE4YmUzNjMyMDNjNmQzYTNkNjFiMWIiOw,,&redirect_uri=https:"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='societe_generale_berlin_group_psd2_pipeline',
        destination='duckdb',
        dataset_name='societe_generale_berlin_group_psd2_data', 
    )
    # Load the data
    load_info = pipeline.run(societe_generale_berlin_group_psd2_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from societe_generale_berlin_group_psd2’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Authorization Endpoint**: This endpoint is used for OAuth2 authorization, allowing users to authenticate and authorize applications to access their data. It supports actions for both signup and signin.
- **Client ID Parameter**: A unique identifier for the client application making the request, essential for the OAuth2 flow.
- **State Parameter**: A value used to maintain state between the request and callback, helping to prevent CSRF attacks.
- **Redirect URI**: The URI to which the user will be redirected after authorization, often used for handling responses from the authorization server.
- **Response Type**: Indicates the type of response expected, typically "code" for authorization code flow.

You will then debug the Société Générale Berlin Group PSD2 API pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Société Générale Berlin Group PSD2 API support.
    ```shell
    dlt init dlthub:societe_generale_berlin_group_psd2 duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Société Générale Berlin Group PSD2 API API, as specified in @societe_generale_berlin_group_psd2-docs.yaml 
    Start with endpoints authorize?client_id=9cf005e2-56c3-4a5d-8546-6dc085eef7f5&state=czozMjI6ImRlZjUwMjAwMzFmMWQ3YzdiYzQzNmU4NjJlMGIwZTQxZjg0ZTJhOTdiYTUyZmZhNzI4MjQ1NGNjMWI4NjhlY2UxNDg2NTE3YTVmMzgzMjUwOWIwYWFkYTI5ZWY2MGM5MDAyZGY1Njc0NTE4NThjOGM3NTEyY2I0NDQ5ODExZmNiMjgyMTJkYjFjMmVkZDI4ZWQxMTA3NDhlMTZiMjc1ODg2YjQ1ZDFiZTY0YTNjNTM0ZTg3NzU5NTYzYmI1ZjRmNDczNWJkNTQzN2MyZTc4Y2Y4NWI4MjlmOWM2NmJlNzU5NzE2YjExMGU4ZWY5MGRmNDc2M2Y5ZjYyYjI1YTVmMzg5N2E3MjVmZTg2OTg4MDg3NGNmODBhYzkxYjhhOWNhMzQyOTIwOGMyYzNhN2ZlNGIxYjk1N2Q5MTUzNGQyYjVjOTkiOw,,&redirect_uri=https: and authorize?client_id=9cf005e2-56c3-4a5d-8546-6dc085eef7f5&state=czozMjI6ImRlZjUwMjAwYWE0MjRmN2Y1ZmMxMTc1MDg2ZDMzN2FhZDI2MDQ3MTZhZGZlMWEyNGE2YjgxYzA4NjY3ZjI0Yzg1Yjk2N2I2Mjg0MzY2ZjU5N2Q3OGQ4NTIxMjRmNTE3NzEwZDEzYTkxYjQyYWZmMDVmODU0NjUyNDQ0MjhiMDgzYzM2MjYzNjEyMDU0N2NiYWZkMzI1YjNmMjIzZmU4YzY3NzA2MmNjOGUyYTdhNDExMmYzN2M5YzFkYmYwZjhmNTNlNTMwMzIxNmFiNTY3YmMzMjYzN2U1NTA4YjIwYjM5MmFmODE3YzhiMzZlOGNmZWMyYzgzNjE3MjgyNWJjOWMzYTA5OGVlMzViMjMwNWYwMzNjYzQ3ZTk4OGFhODQxMWRjZmMyMjkyMjcxYWE4YmUzNjMyMDNjNmQzYTNkNjFiMWIiOw,,&redirect_uri=https: and skip incremental loading for now. 
    Place the code in societe_generale_berlin_group_psd2_pipeline.py and name the pipeline societe_generale_berlin_group_psd2_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python societe_generale_berlin_group_psd2_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    The authentication information required includes a client ID and client secret, both of which are API keys located in the header with the names X-Client-Id and X-Client-Secret, respectively.
    
    To get the appropriate API keys, please visit the original source at https://cash-corporate.developer.societegenerale.lu/product/psd2-v1/api/412.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python societe_generale_berlin_group_psd2_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline societe_generale_berlin_group_psd2 load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset societe_generale_berlin_group_psd2_data
    The duckdb destination used duckdb:/societe_generale_berlin_group_psd2.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline societe_generale_berlin_group_psd2_pipeline show
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("societe_generale_berlin_group_psd2_pipeline").dataset()
   # get "authorize?client_id=9cf005e2-56c3-4a5d-8546-6dc085eef7f5&state=czozMjI6ImRlZjUwMjAwMzFmMWQ3YzdiYzQzNmU4NjJlMGIwZTQxZjg0ZTJhOTdiYTUyZmZhNzI4MjQ1NGNjMWI4NjhlY2UxNDg2NTE3YTVmMzgzMjUwOWIwYWFkYTI5ZWY2MGM5MDAyZGY1Njc0NTE4NThjOGM3NTEyY2I0NDQ5ODExZmNiMjgyMTJkYjFjMmVkZDI4ZWQxMTA3NDhlMTZiMjc1ODg2YjQ1ZDFiZTY0YTNjNTM0ZTg3NzU5NTYzYmI1ZjRmNDczNWJkNTQzN2MyZTc4Y2Y4NWI4MjlmOWM2NmJlNzU5NzE2YjExMGU4ZWY5MGRmNDc2M2Y5ZjYyYjI1YTVmMzg5N2E3MjVmZTg2OTg4MDg3NGNmODBhYzkxYjhhOWNhMzQyOTIwOGMyYzNhN2ZlNGIxYjk1N2Q5MTUzNGQyYjVjOTkiOw,,&redirect_uri=https:" table as Pandas frame
   data."authorize?client_id=9cf005e2-56c3-4a5d-8546-6dc085eef7f5&state=czozMjI6ImRlZjUwMjAwMzFmMWQ3YzdiYzQzNmU4NjJlMGIwZTQxZjg0ZTJhOTdiYTUyZmZhNzI4MjQ1NGNjMWI4NjhlY2UxNDg2NTE3YTVmMzgzMjUwOWIwYWFkYTI5ZWY2MGM5MDAyZGY1Njc0NTE4NThjOGM3NTEyY2I0NDQ5ODExZmNiMjgyMTJkYjFjMmVkZDI4ZWQxMTA3NDhlMTZiMjc1ODg2YjQ1ZDFiZTY0YTNjNTM0ZTg3NzU5NTYzYmI1ZjRmNDczNWJkNTQzN2MyZTc4Y2Y4NWI4MjlmOWM2NmJlNzU5NzE2YjExMGU4ZWY5MGRmNDc2M2Y5ZjYyYjI1YTVmMzg5N2E3MjVmZTg2OTg4MDg3NGNmODBhYzkxYjhhOWNhMzQyOTIwOGMyYzNhN2ZlNGIxYjk1N2Q5MTUzNGQyYjVjOTkiOw,,&redirect_uri=https:".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
