In this guide, we'll set up a complete Cleeng data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def cleeng_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.sandbox.cleeng.com/",
            {
    "auth": {
                    "type": "bearer",
                    "token": access_token,
                }
},
        },
        "resources": [
            login,offers,customers
            ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='cleeng_pipeline',
        destination='duckdb',
        dataset_name='cleeng_data', 
    )
    # Load the data
    load_info = pipeline.run(cleeng_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from cleeng’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Authentication & SSO: Endpoints for customer login, registration, JWT token management, and single sign-on integration with various identity providers. - Payment & Orders: Comprehensive payment processing endpoints supporting multiple gateways (Adyen, PayPal), order management, and various payment methods including in-app purchases. - Subscription Management: Full lifecycle management of subscriptions including creation, updates, switches, pausing, resuming, and termination with support for various billing cycles. - Customer Management: Customer profile management, password resets, consent handling, and customer data operations with GDPR compliance features. - Offers & Pricing: Management of subscription offers, passes, live events, and pricing with support for coupons, localization, and country-specific pricing. - Entitlements & Access: Real-time access validation, entitlement checks, and content access management with grace period support. - In-App Purchases: Comprehensive support for mobile and TV platform purchases including Apple StoreKit 1/2, Google Play, Amazon FireTV, Roku, Samsung TV, and Vizio. - Webhooks & Notifications: Real-time event notifications for subscription lifecycle events, payment status changes, and integration with external systems.

You will then debug the Cleeng pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

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

    Initialize a dlt pipeline with Cleeng support.
    ```shell
    dlt init dlthub:cleeng duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Cleeng API, as specified in @cleeng-docs.yaml 
    Start with endpoints login and offers and skip incremental loading for now. 
    Place the code in cleeng_pipeline.py and name the pipeline cleeng_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python cleeng_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    Cleeng uses multiple authentication methods depending on the integration type. For server-to-server integrations, it uses Publisher Token (48-character API key) passed via X-Publisher-Token header. For client-side integrations, it uses JWT Bearer tokens valid for 15 minutes with refresh tokens valid for 100 days. JWT tokens contain customerId, publisherId, and expiration date. The API also supports API Key authentication for certain endpoints. Publisher tokens should never be exposed publicly as they allow access to sensitive operations like price updates.
    
    To get the appropriate API keys, please visit the original source at https://www.cleeng.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python cleeng_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline cleeng load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset cleeng_data
    The duckdb destination used duckdb:/cleeng.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline cleeng_pipeline show --dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("cleeng_pipeline").dataset()
   # get ogi table as Pandas frame
   data.ogi.df().head()
    ```

## Running into errors?

Cleeng has several important operational considerations: API throttling is implemented with rate limiting at 100 RPS for migrations, JWT tokens expire after 15 minutes requiring refresh token management, orders expire after 24 hours unless payment is captured, IP address limits of 4 per user with 3-hour expiration, caching is strongly recommended for frequently requested data, load testing is strictly prohibited on Cleeng environments, entitlement checks should use accessGranted flag rather than expiresAt due to grace periods, webhook handlers must return 200 status codes to avoid retries, and many JSON-RPC methods are deprecated in favor of REST API 3.1 endpoints.

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)