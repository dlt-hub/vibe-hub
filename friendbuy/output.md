In this guide, we'll set up a complete Friendbuy data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def friendbuy_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://mapi.fbot.me/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "personal_referral_links",
            "referral_links",
            "shares"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='friendbuy_pipeline',
        destination='duckdb',
        dataset_name='friendbuy_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(friendbuy_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from friendbuy’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Personal Referral Links: Allows the creation of personalized referral links for users.
- Referral Links: Retrieve or interact with referral links.
- Shares: Access and manage shared content or links.
- Reminder Emails: Manage or retrieve reminder emails.
- Email Captures: Create or retrieve email captures.
- Purchases: Retrieve information about purchases made.
- Conversions: Manage or retrieve conversion data.
- Rewards: Access information about rewards and manage them.
- Webhooks: Handle incoming data and events through webhooks.

You can combine these endpoints to build pipelines that extract structured content from Friendbuy workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@friendbuy-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Friendbuy support.
    ```shell
    dlt init dlthub:friendbuy duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@friendbuy-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Friendbuy API, as specified in @friendbuy-docs.yaml 
    Start with endpoints "personal_referral_links" and "referral_links" and skip incremental loading for now. 
    Place the code in friendbuy_pipeline.py and name the pipeline friendbuy_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python friendbuy_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The API uses Bearer Token authentication. Tokens are sent in the header of requests. Tokens can be refreshed using a specific refresh token endpoint.
    
    To get appropriate API keys, please visit the original source at https://www.friendbuy.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python friendbuy_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline friendbuy load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset friendbuy_data
    The duckdb destination used duckdb:/friendbuy.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline friendbuy_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("friendbuy_pipeline").dataset()
   # get "personal_referral_links" table as Pandas frame
   data."personal_referral_links".df().head()
    ```

## Running into errors?

Ensure proper handling of refresh tokens for continuous access. Webhooks use signatures for validation; handle them correctly to ensure data security. Be aware of rate limits to avoid request throttling. All GET endpoints require specific time or pagination parameters.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)