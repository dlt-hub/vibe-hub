In this guide, we'll set up a complete Proton Mail data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def proton_mail_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://mail.proton.me/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "pks/lookup",
            "settings/encryption-keys",
            "wallet"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='proton_mail_pipeline',
        destination='duckdb',
        dataset_name='proton_mail_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(proton_mail_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from proton_mail’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Public Key Download: Allows users to download the public key associated with an email address.
- Private Key Download: Provides access to download a user's private encryption keys.
- Wallet: Access to the user's wallet information, including account details.
- Digital Assets: Retrieves information about digital assets owned by the user.

You can combine these endpoints to build pipelines that extract structured content from Proton Mail workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@proton_mail-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Proton Mail support.
    ```shell
    dlt init dlthub:proton_mail duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@proton_mail-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Proton Mail API, as specified in @proton_mail-docs.yaml 
    Start with endpoints "pks/lookup" and "settings/encryption-keys" and skip incremental loading for now. 
    Place the code in proton_mail_pipeline.py and name the pipeline proton_mail_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python proton_mail_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The authentication method used is OAuth2.
    
    To get appropriate API keys, please visit the original source at https://www.proton.me/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python proton_mail_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline proton_mail load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset proton_mail_data
    The duckdb destination used duckdb:/proton_mail.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline proton_mail_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("proton_mail_pipeline").dataset()
   # get "pks/lookup" table as Pandas frame
   data."pks/lookup".df().head()
    ```

## Running into errors?

This source includes advanced features intended for technical users. Users must ensure the safety of their private key passwords and are responsible for backing up their private information. Note that the company does not offer payment or exchange services through this API.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)