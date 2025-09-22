In this guide, we'll set up a complete Home Designs AI data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def home_designs_ai_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://homedesigns.ai/api/v2",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "designscope", "precision"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='home_designs_ai_pipeline',
        destination='duckdb',
        dataset_name='home_designs_ai_data', 
    )
    # Load the data
    load_info = pipeline.run(home_designs_ai_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from home_designs_ai’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **Design Management**: Endpoints related to creating, critiquing, and transferring design elements.
  - `/api/v2/designscope`: Interface for managing design scopes.
  - `/api/v2/design_critique`: Provides critiques on designs submitted.
  - `/api/v2/design_transfer`: Allows transferring design elements to different projects.

- **User Information**: Endpoints for retrieving and managing user-related data.
  - `/api/v2/user_info`: Access user profile and settings.

- **Smart Home Integration**: Endpoints related to smart home functionalities.
  - `/api/v2/smart_home`: Interface for managing smart home devices and settings.

- **File Management**: Endpoints for handling file operations.
  - `/downloadFileFromUrl?url=IMAGE_URL`: Downloads a file from a given URL.

- **Image and Visualization Tools**: Endpoints for creating and visualizing design images and layouts.
  - `/api/v2/create_maskimage`: Generates a mask image for design use.
  - `/api/v2/paint_visualizer`: Visualizes paint colors and textures.
  - `/api/v2/floor_editor`: Tools to edit and design floor layouts.
  - `/api/v2/sky_colors`: Provides color options for skies in designs.

- **Room and Furniture Management**: Endpoints focusing on room composition and furniture details.
  - `/api/v2/room_composer`: Tool for composing and designing room layouts.
  - `/api/v2/furniture_creator`: Create custom furniture designs.
  - `/api/v2/furniture_finder`: Search for furniture items based on criteria.
  - `/api/v2/furniture_removal`: Remove furniture items from a design.
  
- **Material and Texture Customization**: Endpoints for modifying materials and textures.
  - `/api/v2/material_swap`: Swap materials used in designs.
  - `/api/v2/change_color_textures`: Change colors and textures of materials in designs.

- **Advisory and Assistance**: Endpoints for design advice and assistance.
  - `/api/v2/design_advisor`: Provides design advice based on user input.
  - `/api/v2/text_to_design`: Converts text descriptions into design elements.

- **High-Definition Options**: 
  - `/api/v2/full_hd`: Provides high-definition rendering options for designs.

You will then debug the Home Designs AI pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Home Designs AI support.
    ```shell
    dlt init dlthub:home_designs_ai duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Home Designs AI API, as specified in @home_designs_ai-docs.yaml 
    Start with endpoints designscope and precision and skip incremental loading for now. 
    Place the code in home_designs_ai_pipeline.py and name the pipeline home_designs_ai_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python home_designs_ai_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    To authenticate, you need an API access token, which should be included in the Authorization header as 'Bearer {token}' when making requests.
    
    To get the appropriate API keys, please visit the original source at https://api.homedesigns.ai/homedesignsai-api-documentation/api-reference.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python home_designs_ai_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline home_designs_ai load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset home_designs_ai_data
    The duckdb destination used duckdb:/home_designs_ai.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline home_designs_ai_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("home_designs_ai_pipeline").dataset()
   # get "designscope" table as Pandas frame
   data."designscope".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)