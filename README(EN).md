# 🧠 RAG Pipeline — From Data Ingestion to Vector DB

> A complete RAG (Retrieval-Augmented Generation) pipeline for loading, splitting, embedding, and querying documents (PDF & TXT) using LangChain and Ollama.

---

## 📌 Overview

This project implements a complete RAG pipeline that allows you to ask intelligent questions over a collection of text and PDF documents (such as psychology and mental health books like "Man's Search for Meaning" by Viktor Frankl).

The pipeline consists of the following steps:

1. Loading documents from PDF and TXT files
2. Splitting documents into smaller chunks using RecursiveCharacterTextSplitter
3. Converting chunks into embeddings with OllamaEmbeddings
4. Storing embeddings in a vector database
5. Retrieving relevant chunks and generating answers with ChatOllama

The project is built with LangChain and supports multilingual documents (English, Persian, German).

---

## ✨ Features

- 📄 Document loaders: PyMuPDFLoader for PDF, TextLoader for TXT, and DirectoryLoader for folder loading
- ✂️ Recursive text splitting with configurable chunk size and overlap
- 🔢 Embeddings with Ollama (nomic-embed-text model)
- 🗂️ Vector database integration (e.g., FAISS / Chroma)
- 💬 Chat-based question answering with Ollama LLMs (e.g., llama3)
- 🌍 Multilingual document support (English, Persian, German)
- 📓 Jupyter Notebook based — great for development and experimentation
- 🧠 Well-suited for mental health data and psychology texts

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Python 3.11+ | Core language |
| LangChain | RAG orchestration and pipeline management |
| LangChain Community | Document loaders (PDF / TXT) |
| LangChain Ollama | Embeddings and local LLM |
| PyMuPDF | PDF parsing and reading |
| Jupyter Notebook | Development and experimentation environment |
| Ollama | Local LLM runtime |
| FAISS | Fast vector database |

---

## 📁 Project Structure

```
RAG-test/
│
├── data/
│   ├── text_files/          # Text documents (TXT)
│   └── pdf/                 # PDF documents (e.g., doctorfrankel.pdf)
│
├── document.ipynb           # Data Ingestion notebook
├── pdf_loader.ipynb         # Full RAG pipeline notebook
├── requirements.txt         # Python dependencies
└── README.md
```

---

## ⚙️ Installation & Setup

### 1. Clone the repository

```bash
git clone https://github.com/your-username/rag-pipeline.git
cd rag-pipeline
```

### 2. Create a virtual environment

```bash
python -m venv .venv

# Windows
.venv\Scripts\activate

# macOS / Linux
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -U langchain langchain-community langchain-ollama pymupdf jupyter faiss-cpu
```

Or if a requirements.txt file exists:

```bash
pip install -r requirements.txt
```

### 4. Install & run Ollama

First, download and install Ollama from the official site: https://ollama.com

Then pull the required models:

```bash
ollama pull llama3
ollama pull nomic-embed-text
```

Finally, start the Ollama service:

```bash
ollama serve
```

---

## 🚀 Usage

### 1. Prepare your data

Place your PDF files in the data/pdf/ folder and your text files in the data/text_files/ folder.

Example: place the book doctorfrankel.pdf (Man's Search for Meaning) into the data/pdf/ folder.

### 2. Load documents

```python
from langchain_community.document_loaders import PyMuPDFLoader, DirectoryLoader

loader = DirectoryLoader(
    "../data/pdf",
    glob="**/*.pdf",
    loader_cls=PyMuPDFLoader,
)
documents = loader.load()
print(f"Number of loaded documents: {len(documents)}")
```

### 3. Split documents into smaller chunks

```python
from langchain_classic.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    length_function=len,
    separators=["\n\n", "\n", " ", ""]
)
chunks = splitter.split_documents(documents)
print(f"Number of chunks: {len(chunks)}")
```

### 4. Embed and store in a vector database

```python
from langchain_ollama import OllamaEmbeddings
from langchain_community.vectorstores import FAISS

embeddings = OllamaEmbeddings(model="nomic-embed-text")
vectorstore = FAISS.from_documents(chunks, embeddings)
vectorstore.save_local("faiss_index")
```

### 5. Query the LLM (full RAG)

```python
from langchain_ollama import ChatOllama

llm = ChatOllama(model="llama3")
retriever = vectorstore.as_retriever(search_kwargs={"k": 4})

query = "What is logotherapy and what does Frankl say about the meaning of life?"
docs = retriever.invoke(query)
context = "\n\n".join(d.page_content for d in docs)

prompt = f"""Answer the question based on the text below:

Text:
{context}

Question: {query}

Answer:"""

answer = llm.invoke(prompt)
print(answer.content)
```

---

## 🧠 Practical Example on Mental Health Data

This project works by default with psychology and mental health books. For example, you can ask questions such as:

- "How does Frankl explain the meaning of suffering?"
- "What is the relationship between hope and survival in prisoner camps?"
- "How does logotherapy differ from Freudian psychoanalysis?"
- "What is the concept of 'the last of human freedoms' in Man's Search for Meaning?"

---

## 📌 Future Plans

- [ ] Add support for ChromaDB and Pinecone as alternative vector databases
- [ ] Build a web UI with Streamlit or Gradio
- [ ] Add streaming responses for a better user experience
- [ ] Dockerize the project for easier deployment
- [ ] Add RAG evaluation metrics (e.g., RAGAS)
- [ ] Support Hybrid Search
- [ ] Add Conversation Memory

---

## 🤝 Contributing

Contributions to this project are very welcome! To contribute:

1. Fork the repository
2. Create a new branch:
   ```bash
   git checkout -b feature/amazing-feature
   ```
3. Commit your changes:
   ```bash
   git commit -m "Add amazing feature"
   ```
4. Push to your branch:
   ```bash
   git push origin feature/amazing-feature
   ```
5. Open a Pull Request

---

## 📜 License

This project is released under the **MIT License**. The full license text is provided below:

```
MIT License
```
