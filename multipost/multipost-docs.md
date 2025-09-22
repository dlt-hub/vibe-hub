In this guide, we'll set up a complete MultiPost data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def multipost_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.multipost.app/v1/social/rednote",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "profile", "note"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='multipost_pipeline',
        destination='duckdb',
        dataset_name='multipost_data', 
    )
    # Load the data
    load_info = pipeline.run(multipost_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from multipost’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Profile Endpoints**: Access user profiles and related information.
  - `/v1/social/rednote/profile`: Retrieve the profile of the authenticated user.
  - `/v1/social/rednote/profile?user_id=<user_id>`: Retrieve a specific user's profile using their user ID.

- **Note Endpoints**: Manage and retrieve notes.
  - `/v1/social/rednote/note`: Create or retrieve notes.
  - `/v1/social/rednote/note?note_id=<note_id>&share_text=<share_text>`: Retrieve a specific note with sharing options.

- **Search Endpoints**: Search for users or trends.
  - `/v1/social/x/search`: General search endpoint for various criteria.
  - `/v1/social/x/search?keyword=<keyword>&search_type=<type>&cursor`: Search for specific content based on keywords and type.

- **Followers and Followings Endpoints**: Manage user relationships.
  - `/v1/social/x/followers`: Retrieve a list of followers for the authenticated user.
  - `/v1/social/x/followers?screen_name=<screen_name>&cursor`: Retrieve followers of a specific user.
  - `/v1/social/x/followings`: Retrieve a list of users followed by the authenticated user.
  - `/v1/social/x/followings?screen_name=<screen_name>&cursor`: Retrieve followings of a specific user.

- **Trending Endpoints**: Access trending topics or tweets.
  - `/v1/social/x/trending`: Retrieve a list of trending topics.
  - `/v1/social/x/trending?country=<country>`: Retrieve trending topics specific to a country.

- **Tweet Endpoints**: Manage and retrieve tweets and comments.
  - `/v1/social/x/tweet`: Create or post a new tweet.
  - `/v1/social/x/tweet?tweet_id=<tweet_id>`: Retrieve a specific tweet.
  - `/v1/social/x/tweet/comments`: Retrieve comments on a tweet.
  - `/v1/social/x/tweet/comments?tweet_id=<tweet_id>&cursor`: Retrieve comments on a specific tweet.
  - `/v1/social/x/tweets`: Retrieve a list of tweets from the authenticated user.
  - `/v1/social/x/tweets?rest_id=<rest_id>&screen_name=<screen_name>&cursor`: Retrieve tweets from a specific user. 

- **Replies Endpoints**: Access replies to tweets.
  - `/v1/social/x/replies`: Retrieve replies to the authenticated user's tweets.
  - `/v1/social/x/replies?screen_name=<screen_name>&cursor`: Retrieve replies to a specific user's tweets.

You will then debug the MultiPost pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with MultiPost support.
    ```shell
    dlt init dlthub:multipost duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for MultiPost API, as specified in @multipost-docs.yaml 
    Start with endpoints profile and note and skip incremental loading for now. 
    Place the code in multipost_pipeline.py and name the pipeline multipost_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python multipost_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate with the MultiPost API, you need an API Key which you can obtain from the API Key 认证 documentation, and you must include it in the Authorization header as "Authorization: Bearer <Token>" when making requests, where <Token> is the token you receive after authentication.
    
    To get the appropriate API keys, please visit the original source at https://api-docs.multipost.app/285572601e0.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python multipost_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline multipost load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset multipost_data
    The duckdb destination used duckdb:/multipost.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline multipost_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("multipost_pipeline").dataset()
   # get "profile" table as Pandas frame
   data."profile".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)