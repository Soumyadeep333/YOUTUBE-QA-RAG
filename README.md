# YouTube Q&A using RAG

A Retrieval-Augmented Generation (RAG) project that allows users to ask questions about a YouTube video's transcript.

The project extracts a YouTube transcript, cleans and splits the text into smaller chunks, converts the chunks into embeddings using **Google Gemini**, stores them in **FAISS**, retrieves the most relevant information for a question, and finally uses **Gemini** to generate an answer based on the retrieved context.

---

##  Project Overview

Normally, when we ask an LLM a question about a specific YouTube video, the model may not have access to the video's complete transcript.

This project solves that problem using **RAG (Retrieval-Augmented Generation)**.

### Basic Workflow

```text
YouTube Video
      ↓
   Transcript
      ↓
  Text Cleaning
      ↓
  Text Splitting
      ↓
   Embeddings
      ↓
   FAISS Vector Store
      ↓
    Retriever
      ↓
 Relevant Transcript Chunks
      ↓
   Gemini LLM
      ↓
     Answer
```

---

##  Features

* Extracts and processes YouTube video transcripts
* Cleans transcript text by removing timestamps and unnecessary whitespace
* Splits long transcripts into smaller chunks
* Generates vector embeddings using Google Gemini
* Stores embeddings using FAISS
* Performs similarity-based retrieval
* Uses retrieved transcript context to answer questions
* Uses LangChain to build the RAG pipeline
* Implemented in Google Colab

---

##  Technologies Used

* **Python**
* **Google Gemini**
* **Gemini Embeddings**
* **LangChain**
* **FAISS**
* **YouTube Transcript API**
* **Google Colab**

### Main Libraries

```text
youtube-transcript-api
langchain-community
langchain-google-genai
langchain-text-splitters
faiss-cpu
tiktoken
python-dotenv
```

---

##  How the RAG Pipeline Works

### 1. Transcript Ingestion

The first step is obtaining the transcript of the YouTube video.

The transcript is then converted into plain text so that it can be processed by the RAG pipeline.

---

### 2. Text Cleaning

The transcript may contain timestamps and unnecessary whitespace.

For example:

```text
0:00 Hello everyone
0:07 Today we are discussing AI
0:14 Let's understand how AI works
```

After cleaning:

```text
Hello everyone Today we are discussing AI Let's understand how AI works
```

The project uses regular expressions to remove timestamps and normalize whitespace.

---

### 3. Text Splitting

Long transcripts cannot always be efficiently processed as a single piece of text.

The transcript is therefore divided into smaller chunks using:

**RecursiveCharacterTextSplitter**

Current configuration:

```text
Chunk Size   = 1000
Chunk Overlap = 200
```

The overlap helps preserve context between neighboring chunks.

```text
Chunk 1
┌──────────────────────────────┐
│ Text Text Text Text Text     │
│ Text Text Text Text Text     │
└───────────────┬──────────────┘
                │ overlap
                ▼
        Chunk 2
┌──────────────────────────────┐
│ Text Text Text Text Text     │
│ Text Text Text Text Text     │
└──────────────────────────────┘
```

---

### 4. Embedding Generation

Each text chunk is converted into a numerical vector using Google's Gemini embedding model.

```text
Text Chunk
    ↓
Gemini Embedding Model
    ↓
Numerical Vector
```

The project uses:

```text
models/gemini-embedding-001
```

These vectors allow the system to compare the semantic similarity between a user's question and transcript chunks.

---

### 5. Vector Storage with FAISS

The generated embeddings are stored in a **FAISS vector store**.

FAISS allows the system to efficiently search for transcript chunks that are semantically similar to the user's question.

```text
Transcript Chunks
      ↓
   Embeddings
      ↓
     FAISS
      ↓
Similarity Search
```

---

### 6. Retrieval

When a user asks a question, the question is converted into an embedding.

The retriever then searches FAISS for the most relevant transcript chunks.

The current retriever configuration retrieves:

```text
Top K = 4
```

So the four most relevant chunks are passed to the next stage.

---

### 7. Prompt Construction

The retrieved documents are combined into a context.

The system then creates a prompt containing:

```text
Context
   +
User Question
   ↓
Prompt
```

This gives Gemini the relevant information from the YouTube transcript before generating the answer.

---

### 8. Answer Generation

Finally, the retrieved context and user's question are passed to the Gemini chat model.

```text
Retrieved Context
       +
User Question
       ↓
     Gemini
       ↓
Generated Answer
```

The project also uses LangChain's runnable components to build the complete chain:

```text
Retriever
   ↓
Format Documents
   ↓
Prompt
   ↓
Gemini
   ↓
Output Parser
   ↓
Final Answer
```

---

##  Project Structure

```text
youtube-qa-rag/
│
├── Youtube_Q&A_USING_RAG.ipynb
├── README.md
├── requirements.txt
|
```

### Main File

`Youtube_Q&A_USING_RAG.ipynb`

This Google Colab notebook contains the complete RAG implementation.

---

##  API Key Setup

This project uses the **Google Gemini API**.

The API key should **never be directly written inside the notebook or uploaded to GitHub**.

In Google Colab, the project retrieves the API key from Colab Secrets:

```python
from google.colab import userdata

os.environ["GOOGLE_API_KEY"] = userdata.get("GOOGLE_API_KEY")
```

### Setting up the key in Google Colab

1. Open the notebook in Google Colab.
2. Open the **Secrets** section.
3. Add a new secret named:

```text
GOOGLE_API_KEY
```

4. Add your Gemini API key as its value.
5. Enable notebook access to the secret.

---

##  How to Run

### Option 1: Google Colab

1. Open the notebook:

```text
Youtube_Q&A_USING_RAG.ipynb
```

2. Open it in Google Colab.
3. Configure your `GOOGLE_API_KEY` in Colab Secrets.
4. Install the required dependencies.
5. Run the notebook cells sequentially.
6. Provide a question related to the transcript.
7. The RAG pipeline retrieves relevant information and generates an answer.

---

##  Example Questions

You can ask questions such as:

```text
What is the main topic discussed in the video?
```

```text
What does the speaker say about AI agents?
```

```text
What MIT study is mentioned in the video?
```

```text
What does the speaker say about the future of classrooms?
```

```text
Can you summarize the video?
```

The system searches the transcript and generates an answer based on the retrieved context.

---

##  Complete Architecture

```text
                ┌─────────────────┐
                │  YouTube Video  │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │    Transcript   │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │  Text Cleaning  │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │  Text Splitting │
                │  1000 / 200     │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │ Gemini Embedding│
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │      FAISS      │
                │  Vector Store   │
                └────────┬────────┘
                         │
                 User Question
                         ↓
                ┌─────────────────┐
                │    Retriever    │
                │      Top-4      │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │ Relevant Context│
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │  Gemini LLM     │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │  Final Answer   │
                └─────────────────┘
```

---

## What I Learned From This Project

This project helped me understand and implement the major components of a RAG system:

* Document ingestion
* Transcript processing
* Text cleaning
* Text chunking
* Embeddings
* Vector databases
* Similarity search
* Retrieval
* Prompt construction
* LLM generation
* LangChain Runnable chains
* FAISS
* Gemini API integration

---

##  Future Improvements

Possible improvements for this project include:

* Support multiple YouTube videos
* Automatically extract transcripts from YouTube URLs
* Add timestamps to retrieved answers
* Add a Streamlit user interface
* Add conversation memory
* Improve retrieval using hybrid search
* Add reranking
* Store and reuse FAISS indexes
* Add source references for answers
* Deploy the application as a web application

---

##  Author

**Soumyadeep Datta**

M.Tech Artificial Intelligence & Data Science
NIT Durgapur

---



Feel free to explore the notebook, experiment with different YouTube transcripts, and improve the RAG pipeline.

