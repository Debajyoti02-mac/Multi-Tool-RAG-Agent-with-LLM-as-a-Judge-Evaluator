# Multi-Tool RAG Agent with LLM-as-a-Judge Evaluator

An advanced Retrieval-Augmented Generation (RAG) pipeline built with LangChain and Groq. This AI agent utilizes dynamic tool routing to seamlessly switch between local document retrieval, web search fallbacks, and safe mathematical calculations. It also features a built-in automated evaluator to score its own responses for hallucination and factual grounding.

## 🚀 Key Features

*   **Hybrid Retrieval System:** Queries a local ChromaDB vector database first. If the semantic distance exceeds the strict relevance threshold (0.75), it automatically falls back to the open internet using DuckDuckGo Search.
*   **Dynamic Tool Routing:** Uses `llama-3.1-8b-instant` to analyze user prompts and route them to the appropriate tool (Document Search, Web Search, or Calculator) without hardcoded if/else logic.
*   **LLM-as-a-Judge Evaluation:** Implements a strict Pydantic schema to automatically evaluate final generated answers. The judge verifies if the agent's answer is `fully_grounded`, `partial_grounded`, or `hallucinated` based strictly on the retrieved context.
*   **Secure Execution:** Designed with security in mind, specifically avoiding `eval()` in mathematical parsing to prevent Arbitrary Code Execution (ACE) vulnerabilities.

## 🛠️ Tech Stack

*   **LLM:** Llama-3.1-8b-instant (via Groq API)
*   **Framework:** LangChain
*   **Vector Database:** ChromaDB (Persistent)
*   **Embeddings:** SentenceTransformers (`all-MiniLM-L6-v2` or similar)
*   **Data Parsing:** PyPDFLoader, RecursiveCharacterTextSplitter
*   **Evaluation:** Structured Outputs with Pydantic

## 🧠 Architecture Flow

1.  **Ingestion:** A local PDF is chunked (1400 tokens / 120 overlap) and embedded into ChromaDB.
2.  **Routing:** The user submits a query. The LLM decides whether to use a tool or answer directly.
3.  **Execution:** 
    *   *Math query?* -> Routes to the Calculator tool.
    *   *Knowledge query?* -> Routes to ChromaDB. If context is poor, falls back to DuckDuckGo.
4.  **Generation:** The agent synthesizes the raw tool output into a conversational response.
5.  **Evaluation:** A separate, strictly prompted LLM acts as a judge, analyzing the original query, the raw tool context, and the final answer to output a deterministic JSON score.

