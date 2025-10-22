In this guide, we'll set up a complete Nightbot data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def nightbot_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.nightbot.tv/1/song_requests/queue",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "skip", "play"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='nightbot_pipeline',
        destination='duckdb',
        dataset_name='nightbot_data', 
    )
    # Load the data
    load_info = pipeline.run(nightbot_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from nightbot’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Channel Endpoints**: Manage and interact with channels.
  - `/1/channel/send`: Send messages to a channel.
  - `/1/channel/join`: Join a specified channel.
  - `/1/channel`: Get channel details.

- **Song Requests Endpoints**: Handle song requests and playlists.
  - `/1/song_requests/queue`: View the current queue of song requests.
  - `/1/song_requests/queue/:id/promote`: Promote a specific song request in the queue.
  - `/1/song_requests/playlist/:id`: Access a specific playlist of song requests.

- **Timers Endpoints**: Manage timers for various functions.
  - `/1/timers`: List all timers.
  - `/1/timers/:id`: Access specific timer details.

- **Spam Protection Endpoints**: Manage spam protection settings.
  - `/1/spam_protection`: Access spam protection settings.

- **Commands Endpoints**: Manage default commands.
  - `/1/commands/default/:name`: Access specific default command details.

- **OAuth2 Endpoints**: Handle authentication tokens.
  - `/oauth2/token`: Request a new OAuth2 token.
  - `/oauth2/token/revoke`: Revoke an existing OAuth2 token.

You will then debug the Nightbot pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Nightbot support.
    ```shell
    dlt init dlthub:nightbot duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Nightbot API, as specified in @nightbot-docs.yaml 
    Start with endpoints skip and play and skip incremental loading for now. 
    Place the code in nightbot_pipeline.py and name the pipeline nightbot_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python nightbot_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    Nightbot uses the OAuth 2 flow for securely authorizing requests to its API.
    
    To get the appropriate API keys, please visit the original source at https://api-docs.nightbot.tv/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python nightbot_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline nightbot load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset nightbot_data
    The duckdb destination used duckdb:/nightbot.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline nightbot_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("nightbot_pipeline").dataset()
   # get "skip" table as Pandas frame
   data."skip".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
