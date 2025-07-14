In this guide, we'll set up a complete RentCast data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def rentcast_real_estate_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.rentcast.io/v1/",
            "auth": {
                "type": "api_key",
                "token": access_token,
            },
        },
        "resources": [
            "properties",
            "property_details",
            "owner_information"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='rentcast_real_estate_pipeline',
        destination='duckdb',
        dataset_name='rentcast_real_estate_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(rentcast_real_estate_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from rentcast_real_estate’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Properties: Retrieve details about properties based on city or state.
- Property Details: Fetch detailed information about a specific property by address.
- Owner Information: Get information about the owner of a specific property.
- Rent Estimates: Provides rent estimates for a given address.
- Sales Comparables: Access sales comparable data for a property.
- Rental Listings: List rental properties based on city and zip code.
- Market Trends: View market trend data by zip code.
- Market Statistics: Gather detailed market statistics and data for specific zip codes.

You can combine these endpoints to build pipelines that extract structured content from RentCast workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@rentcast_real_estate-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with RentCast support.
    ```shell
    dlt init dlthub:rentcast_real_estate duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for RentCast API, as specified in @rentcast_real_estate-docs.yaml 
    Start with endpoints "properties" and "property_details" and skip incremental loading for now. 
    Place the code in rentcast_real_estate_pipeline.py and name the pipeline rentcast_real_estate_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python rentcast_real_estate_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via API keys, which must be included in the header of each request. The key should be kept secure to prevent unauthorized access.
    
    To get appropriate API keys, please visit the original source at https://www.rentcast.io/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python rentcast_real_estate_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline rentcast_real_estate load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset rentcast_real_estate_data
    The duckdb destination used duckdb:/rentcast_real_estate.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline rentcast_real_estate_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("rentcast_real_estate_pipeline").dataset()
   # get properties table as Pandas frame
   data.properties.df().head()
    ```

## Running into errors?

API access is restricted if payment fails. Free plan limits to 50 requests per month. Data coverage and accuracy might vary depending on the availability of active listings. Historical data is only available from specific dates, and updates occur daily. Coverage is extensive but does not include non-residential commercial properties. Rate limits apply, and pagination requires consistent query parameters.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)