In this guide, we'll set up a complete Atlassian Confluence data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def confluence_wiki_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.atlassian.net/wiki/rest/api/v2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "blogposts",
            "pages",
            "spaces"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='confluence_wiki_pipeline',
        destination='duckdb',
        dataset_name='confluence_wiki_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(confluence_wiki_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from confluence_wiki’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Blogposts: Fetch blog posts within Confluence.
- Pages: Retrieve pages from Confluence spaces.
- Spaces: Access information about spaces in Confluence.

You can combine these endpoints to build pipelines that extract structured content from Atlassian Confluence workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@confluence_wiki-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Atlassian Confluence support.
    ```shell
    dlt init dlthub:confluence_wiki duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Atlassian Confluence API, as specified in @confluence_wiki-docs.yaml 
    Start with endpoints "blogposts" and "pages" and skip incremental loading for now. 
    Place the code in confluence_wiki_pipeline.py and name the pipeline confluence_wiki_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python confluence_wiki_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via OAuth2 with a refresh token flow. Client credentials and tokens are used in the header for requests.
    
    To get appropriate API keys, please visit the original source at https://www.atlassian.com/software/confluence.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python confluence_wiki_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline confluence_wiki load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset confluence_wiki_data
    The duckdb destination used duckdb:/confluence_wiki.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline confluence_wiki_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("confluence_wiki_pipeline").dataset()
   # get blogposts table as Pandas frame
   data.blogposts.df().head()
    ```

## Running into errors?

Ensure compatibility with version 1.0.0, and schema modifications may require adjustments in existing integrations. Common errors include 400 Bad Request for incorrect parameters, 401 Unauthorized for token or scope issues, and 404 Not Found for incorrect endpoint paths.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)