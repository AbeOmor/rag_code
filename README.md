# RAG Studio Demo Code

Note: This feature is in [Private Preview](https://docs.databricks.com/en/release-notes/release-types.html). To try it, reach out to your Databricks contact or [rag-feedback@databricks.com](mailto:rag-feedback@databricks.com).

# Documentation

We suggest following the below tutorials to get started, but you can also refer to our documentation:
- [RAG Studio Overview Docs.pdf](./RAG Studio Overview Docs.pdf)
- [Evaluation Suite Doc.pdf](./Evaluation Suite Doc.pdf)

# Tutorials

Important: Throughout the tutorial notebooks you will need to replace the `PUT_*` placeholders with the URLs you recieved from your Databricks representative.

`%pip install --quiet "PUT_RAG_EVAL_SUITE_WHEEL_HERE"`
`%pip install --quiet "PUT_RAG_STUDIO_WHEEL_HERE"`

## Tutorial 1: Hello world

### Creating a chain
First, let's create the most minimal chain in RAG Studio.  This tutorial will familarize you with the basics of the development workflow and the chain input/output schema.

When using RAG Studio, you create your chain code using LangChain inside a Notebook.  Open the `hello_world_chain` Notebook, review the commented code, and try running it.

Note: If you need support for Python based chains, please reach out to our team.

### Logging & deploying a chain

Now, let's log this chain to MLflow and deploy it to the Review App.

When using RAG Studio, you create, evaluate, and deploy new versions of your chain using a "Driver" notebook.  For now, let's just log and deploy the chain.  Open the `1_hello_world_driver_notebook` Notebook, review the code, and try running it.

If successful, you will be able to chat with your hello world chain in the Review App.

## Tutorial 2: Hello World w/ parameterization

### Parameterizing a chain

RAG Studio supports parameterizing your chains - this allows you to quickly iterate on quality related parameters (such as the prompt or retriever configuruation) while holding the chain's code constant.

Parameterization is based on a YAML file.  Open `2_hello_world_config.yaml` for an example of the configuration file - the structure of the YAML is up to you, although in the next tutorial, you will see our suggested structure.

Open `2_hello_world_parameterized_chain` and `2_hello_world_parameterized_chain_driver_notebook` to see how this works in practice.

How to access configuration settings:
```
############
# Get the configuration YAML
############
rag_config = RagConfig("1_hello_world_config.yaml")
rag_config.get('sample_param')
```

## Tutorial 3: Creating & evaluating a RAG chain

NOw, we will create a simple RAG chain with PDF files from a UC Volume.  

### Create a UC Volume with PDF files

Create a UC Volume and load PDF files.  You can use the PDFs from the directory `sample_pdfs` to get started quickly - these PDFs are a few recent research papers from Matei's lab.

### Create a Vector Index

Open and follow the steps in the notebook `3_load_pdf_to_vector_index` to load PDF files from the UC Volume to a Vector Index.  The sample notebook uses the BGE embedding model hosted on FMAPI, but later tutorials show you how to use OpenAI's embedding models.

### Prototype a RAG Chain

1. Take the output from the last cell in the `3_load_pdf_to_vector_index` notebook and overwrite the first few lines of the `3_rag_chain_config.yaml` configuration so your chain can use the vector index you just created.

2. Open the notebook `3_rag_chain` and run the code locally to test the chain.  This chain uses the `Databricks-DBRX-Instruct` model hosted on FMAPI.

3. Open the notebook `3_rag_chain_driver_notebook` to log, evaluate, and deploy the chain.

# Advanced examples & tutorials

## 4. Multi-turn converastion

The chain `4_rag_chain_w_conversation_history` and `4_rag_chain_w_conversation_history_config.yaml` is an example showing you how to enable multi-turn conversation with a query re-writer prompt.  You can use this example with the driver notebook from Tutorial #3.

## 5. Advanced Evaluation
### 5a. Using RAG Evaluation Suite without RAG Studio

If you have a RAG chain that was deployed outside of RAG Studio, you can still use the Evaluation Suite to assess the chain's quality.  See `5_evaluation_without_rag_studio` to see how to do this.

### 5b. Improving LLM judge accuracy with few-shot examples

To improve the accuracy of the Databricks judges, you can provide few-shot examples of "good" and "bad" answers for each LLM judge.  Databricks strongly reccomends providing at least 2 postive and 2 negative examples per judge to improve the accuracy.  See the bottom of the notebook `5_evaluation_without_rag_studio` for how to do this.  Note: Even though this example configuration is included in the non-RAG Studio evaluation example, you can use the example configuration with the RAG Studio evaluation tutorials above.

## 6. Review user feedback from the Review App

You can use the human feedback collected using the Review App to:
- Determine where you chain is working well and/or not working
- Curate an evaluation set for offline evaluation
- Fine tune a generation or embedding model to improve quality

All deployed models from RAG Studio automatically collect trace logs and human feedback to an Inference Table.  Use the notebook `6_export_inference_table_to_logs` to turn this Inference Table into a well-schemed `request_log` (with traces) and `assessment_log` with human feedback.