In this guide, we'll set up a complete USDA FoodData Central data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def food_data_central_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.nal.usda.gov/fdc/v1/",
            "auth": {
                "type": "apikey",
                "location": "query",
                "header_name": "api_key"
            },
        },
        "resources": [
            "food_details",
            "foods_list",
            "foods_search"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='food_data_central_pipeline',
        destination='duckdb',
        dataset_name='food_data_central_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(food_data_central_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from food_data_central’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Food Details: Retrieve detailed information about a specific food item using its FDC ID. - Foods List: Access a list of all foods available in the FoodData Central database. - Foods Search: Search for foods based on query parameters like ingredients, nutrients, and more. - Branded Foods: Specific category for commercially branded food items. - Foundation Foods: Foods with expanded nutrient profiles and metadata, crucial for detailed dietary studies. - Legacy Foods: Traditional USDA food information, older data before the new FoodData Central system. - Experimental Foods: Foods under experimental analysis, potentially newer or less confirmed data. - Food Descriptions: General descriptions of food items. - Nutrient Data: Detailed nutrient information for food items. - Weights: Weights and measures for various food items.

You can combine these endpoints to build pipelines that extract structured content from USDA FoodData Central workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@food_data_central-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with USDA FoodData Central support.
    ```shell
    dlt init dlthub:food_data_central duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@food_data_central-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for USDA FoodData Central API, as specified in @food_data_central-docs.yaml 
    Start with endpoints "food_details" and "foods_list" and skip incremental loading for now. 
    Place the code in food_data_central_pipeline.py and name the pipeline food_data_central_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python food_data_central_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done using an API key that must be included in the query parameters of each request.
    
    To get appropriate API keys, please visit the original source at https://www.usda.gov/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python food_data_central_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline food_data_central load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset food_data_central_data
    The duckdb destination used duckdb:/food_data_central.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline food_data_central_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("food_data_central_pipeline").dataset()
   # get "food_details" table as Pandas frame
   data."food_details".df().head()
    ```

## Running into errors?

Limitations include a rate limit of 1,000 requests per hour per IP address. The mobile view provides only a basic view of search results, and advanced filtering features are available only on desktop views.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)