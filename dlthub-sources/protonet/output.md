In this guide, we'll set up a complete Keras Applications data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def keras_applications_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "~/.keras/",
            "auth": {
                "type": "bearer",
                "token": access_token
            },
        },
        "resources": [
            "MobileNet",
            "MobileNetV2",
            "MobileNetV3Small"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='keras_applications_pipeline',
        destination='duckdb',
        dataset_name='keras_applications_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(keras_applications_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from keras_applications’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Model Access: Provides endpoints to access various machine learning models, specifically MobileNet versions and other related models.
- Data Access: Endpoints to access training and testing datasets, including CIFAR-10 and image inference datasets.
- Model Operations: Supports operations such as training, predictions, and obtaining model summaries.

You can combine these endpoints to build pipelines that extract structured content from Keras Applications workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@keras_applications-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Keras Applications support.
    ```shell
    dlt init dlthub:keras_applications duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@keras_applications-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Keras Applications API, as specified in @keras_applications-docs.yaml 
    Start with endpoints "MobileNet" and "MobileNetV2" and skip incremental loading for now. 
    Place the code in keras_applications_pipeline.py and name the pipeline keras_applications_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python keras_applications_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses OAuth2 with refresh token flow which requires setup of a connected application in the API. The authentication involves acquiring an access token using a refresh token, client ID, and client secret.
    
    To get appropriate API keys, please visit the original source at https://keras.io/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python keras_applications_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline keras_applications load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset keras_applications_data
    The duckdb destination used duckdb:/keras_applications.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline keras_applications_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("keras_applications_pipeline").dataset()
   # get "MobileNet" table as Pandas frame
   data."MobileNet".df().head()
    ```

## Running into errors?

Requires TensorFlow 2.9 or higher for certain models. Saving to TF SavedModel format via model.save() is no longer supported in Keras 3. Ensure careful monitoring to avoid overfitting.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)