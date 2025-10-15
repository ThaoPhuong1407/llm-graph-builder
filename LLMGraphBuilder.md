##Option 1
####Use the demo version of the app [here](https://llm-graph-builder.neo4jlabs.com/).

1. Create a Neo4j Account
[Sign up for a Neo4j account](https://login.neo4j.com/u/login/identifier?state=hKFo2SBVNjh1cGs2QjRnSHZQV1ZDN050dzR2TERFWTlzWW5hOKFur3VuaXZlcnNhbC1sb2dpbqN0aWTZIGg4ekppRW9aR0FkOUZWaEdFRDJEaEp1WS1zR2lIcUFmo2NpZNkgV1NMczYwNDdrT2pwVVNXODNnRFo0SnlZaElrNXpZVG8).
3. Once signed in, create a new Neo4j instance via the Neo4j Console. 
	* When creating the Neo4j instance, you’ll set a database password. Make sure to securely store this password, as it will be required later to connect to the app.
4. Connect to the Demo App
	* Once the Neo4j instance is up and running, navigate to the demo version of the app.
	* Fill in the required connection details (e.g., URI, username, and password).
	* Start uploading your data to generate graphs.

#####Notes
* Data Limitations: The demo version has processing limitations due to restricted LLM usage. For larger datasets or higher flexibility, consider running the app locally (see Option 2).

##Option 2
####Run a local llm-graph-builder, using your own LLM key.

#####Prerequisites
* Ensure you have Node.js installed (v16 or later is recommended).
* A Neo4j instance set up locally or via the cloud. (Refer to Neo4j Setup Guide)
* An API key from your LLM provider (e.g., OpenAI API key).

#####Steps 
1. Clone the Repository
```https://github.com/ThaoPhuong1407/llm-graph-builder.git
```
```
cd llm-graph-builder  
```

2. Follow the Instructions. Refer to the [LLM Graph Builder README](https://github.com/neo4j-labs/llm-graph-builder/blob/main/README.md) for detailed setup instructions. 

#####Notes
* For Neo4j Cloud DB Users: Use the docker-compose method mentioned in the README.
* For Neo4j Local DB Users:
	* Follow the **Running Backend and Frontend Separately (Dev Environment)** section.
	* Ensure you have a Neo4j Database version 5.15 or later with APOC installed.
	* Update your .env file to fix a typo in the LLM model configuration. Change: `LLM_MODEL_CONFIG_openai_gpt_3.5` to `LLM_MODEL_CONFIG_openai-gpt-3.5` (This typo occurs because the string copied from the UI uses dashes - instead of underscores _, leading to errors.)

![]()