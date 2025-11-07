In this guide, we'll set up a complete Spotify data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def spotify_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.spotify.com/v1/playlists/3cEYpjA9oz9GiPac4AsH4n",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "followers", "images"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='spotify_pipeline',
        destination='duckdb',
        dataset_name='spotify_data', 
    )
    # Load the data
    load_info = pipeline.run(spotify_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from spotify’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **User Profile Endpoints**: Access personal user information and details.
  - `/v1/me`: Retrieve the current user's profile.
  - `/v1/users/exampleuser`: Fetch details for a specific user.

- **Show Endpoints**: Manage and view shows related to the user.
  - `/v1/me/shows?offset=0&limit=20`: Get a list of shows for the user with pagination.
  - `/v1/me/shows?offset=1&limit=1`: Retrieve a specific show from the user's list.

- **Artist Endpoints**: Access information about artists and their works.
  - `/v1/artists/4Z8W4fKeB5YxbusRsdQVPb`: Fetch details for a specific artist.
  - `/v1/artists/1vCWHaC5f2uS3yhpwWbIA6/albums?album_type=SINGLE&offset=20&limit=10`: Get a list of singles by a specific artist.

- **Track Endpoints**: Information and details about individual tracks.
  - `/v1/tracks/2TpxZ7JUBn3uw46aR7qd6V`: Retrieve details for a specific track.
  - `/v1/tracks/6kLCHFM39wkFjOuyPGLGeQ`: Fetch another specific track.
  - `/v1/tracks/6kLCHFM39wkFjOuyPGLGeQ?market=US`: Get track details with market-specific information.

- **Album Endpoints**: Access details about albums and their contents.
  - `/v1/albums/4aawyAB9vmqN3uQ7FjRGTy`: Retrieve a specific album's details.
  - `/v1/albums/4aawyAB9vmqN3uQ7FjRGTy/tracks`: Get a list of tracks in a specific album.
  - `/v1/albums?ids=...`: Fetch multiple albums by their IDs. 

- **Browse Categories Endpoint**: Explore different categories available in the service.
  - `/v1/browse/categories`: Retrieve a list of available categories for browsing.

You will then debug the Spotify pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Spotify support.
    ```shell
    dlt init dlthub:spotify duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Spotify API, as specified in @spotify-docs.yaml 
    Start with endpoints followers and images and skip incremental loading for now. 
    Place the code in spotify_pipeline.py and name the pipeline spotify_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python spotify_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    You will need an Access Token, which you can learn about in the [Access Token](https://documentation/web-api/concepts/access-token) documentation; additionally, you can explore the [Authorization code](https://documentation/web-api/tutorials/code-flow) and [Authorization code PKCE](https://documentation/web-api/tutorials/code-pkce-flow) flows for obtaining tokens.
    
    To get the appropriate API keys, please visit the original source at https://developer.spotify.com/documentation/web-api/reference/get-categories.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python spotify_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline spotify load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset spotify_data
    The duckdb destination used duckdb:/spotify.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline spotify_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("spotify_pipeline").dataset()
   # get "followers" table as Pandas frame
   data."followers".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
