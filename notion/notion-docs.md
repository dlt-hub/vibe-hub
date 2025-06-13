# How to load Notion data in Python using dlt

**Build a Notion-to-database or-dataframe pipeline in Python using dlt with automatic cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```yaml
import dlt
from dlt.sources.rest_api import rest_api_source

def load_notion() -> None:
    pipeline = dlt.pipeline(
        pipeline_name="rest_api_notion",
        destination="duckdb",
        dataset_name="notion_data",
    )

    notion_source = rest_api_source(
        {
            "client": {
                "base_url": "https://api.notion.com/v1"
            },
            "resources": [
                "/comments",
                "/databases",
                "/users",
            ],
        }
    )

    ...

    load_info = pipeline.run(notion_source)
    print(load_info)
```

Why use dlt for this?

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from Notion’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Pages & Properties**: read, update, and retrieve page content and metadata
- **Blocks**: manage blocks and nested content structures
- **Databases**: query, edit, and introspect databases and their schemas
- **Comments**: create, read, and respond to page-level comments

You can combine these endpoints to build pipelines that extract structured content from Notion workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    ```python
    pip install dlt[duckdb]
    dlt init vibe:notion duckdb
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: (let’s generate prompt for 1-2 endpoints only). 
    
    ```
    Please generate REST API Source for Notion API documented in @notion_spec.yaml
    and place it in file `**notion_pipeline.py**`. Start with **docs** endpoint and skip incremental
    loading.
    Make it easy to add any passwords or tokens.
    Use @github_pipeline as an example. Also refer to dlt @documentation
    
    ```
    
    <aside>
    💡
    
    1. prompt must contain valid context: @documentation.yml @example_pipeline.py @restapi_doc_page, where yaml documentation is the minimum 
    2. prompt should contain a name where pipeline script will be created so we can run it below. LLM will handle this
    </aside>
    
3. **Setup credentials** 
    
    The Notion API uses **bearer token authentication**. You’ll need to securely store and pass your token in the `Authorization` header. The token must be included in every request to access protected resources.
    
    Within your generated code, you will be asked to put in your API key and other keys (where needed). For example, like this:
    
    ```python
    if __name__ == "__main__":
        # Replace these with your actual Notion API key and database ID
        NOTION_API_KEY = "<your-api-key>"
        NOTION_DATABASE_ID = "<your-database-id>"
        
        run_notion_pipeline(NOTION_API_KEY, NOTION_DATABASE_ID) 
    ```
    
    If you want to protect these secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```
    python notion_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```python
    Pipeline notion load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset notion_data
    The duckdb destination used duckdb:/notion.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```python
    dlt pipeline notion_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("notion_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```
    

## Running into errors?

Here are some things that may help:

### 📬 Webhooks & Event Handling

- Webhook **event delivery is aggregated**, may arrive out of order, and may be delayed by up to one minute.
- Events include types like `page_content_updated`, `comment_created`, and `database_schema_updated`.
- Notion **retries delivery up to 8 times** with exponential backoff.
- Events require timestamp-based ordering client-side.
- Only one of `parent.page_id` or `discussion_id` may be passed in comment endpoints.

### 🧠 Integration Behavior & Edge Cases

- **Unsupported block types** are returned as `"unsupported"`.
- Comments can only be attached to **pages or discussion threads**, not all object types.
- User object detail level depends on integration scopes.
- **Public endpoints (like link previews)** do not require authentication.
- Some endpoints are **deprecated** and may return incomplete or unstable data (e.g., old search endpoints).
- Relationships (relations, rollups) may behave inconsistently if not properly shared or aggregated.

### Extra resources:

- L[earn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi
- https://www.notion.com/help/guides/category/documentation
- https://dlthub.com/docs/walkthroughs/deploy-a-pipeline