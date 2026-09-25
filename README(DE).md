[🇮🇷 فارسی](./README.md) | [🇬🇧 English](./README(EN).md) | [🇩🇪 Deutsch](./README(DE).md)

# 🧠 RAG-Pipeline — Von der Datenerfassung bis zur Vektordatenbank

> Eine vollständige RAG-Pipeline (Retrieval-Augmented Generation) zum Laden, Aufteilen, Einbetten und Abfragen von Dokumenten (PDF & TXT) mit LangChain und Ollama.

---

## 📌 Übersicht

Dieses Projekt implementiert eine vollständige RAG-Pipeline, mit der Sie intelligente Fragen zu einer Sammlung von Text- und PDF-Dokumenten stellen können (z. B. Psychologie- und psychische Gesundheit-Bücher wie „… trotzdem Ja zum Leben sagen" von Viktor Frankl).

Die Pipeline besteht aus folgenden Schritten:

1. Laden von Dokumenten aus PDF- und TXT-Dateien
2. Aufteilen der Dokumente in kleinere Abschnitte mit RecursiveCharacterTextSplitter
3. Umwandlung der Abschnitte in Embeddings mit OllamaEmbeddings
4. Speichern der Embeddings in einer Vektordatenbank
5. Abrufen relevanter Abschnitte und Generierung von Antworten mit ChatOllama

Das Projekt basiert auf LangChain und unterstützt mehrsprachige Dokumente (Englisch, Persisch, Deutsch).

---

## ✨ Funktionen

- 📄 Dokumentenlader: PyMuPDFLoader für PDF, TextLoader für TXT und DirectoryLoader für Ordner
- ✂️ Rekursive Textaufteilung mit konfigurierbarer Chunk-Größe und Überlappung
- 🔢 Embeddings mit Ollama (Modell nomic-embed-text)
- 🗂️ Integration einer Vektordatenbank (z. B. FAISS / Chroma)
- 💬 Chat-basierte Fragebeantwortung mit Ollama LLMs (z. B. llama3)
- 🌍 Mehrsprachige Dokumentenunterstützung (Englisch, Persisch, Deutsch)
- 📓 Jupyter-Notebook-basiert — ideal für Entwicklung und Experimente
- 🧠 Gut geeignet für Daten zur psychischen Gesundheit und Psychologie-Texte

---

## 🛠️ Technologie-Stack

| Tool | Zweck |
|------|-------|
| Python 3.11+ | Kernsprache |
| LangChain | RAG-Orchestrierung und Pipeline-Verwaltung |
| LangChain Community | Dokumentenlader (PDF / TXT) |
| LangChain Ollama | Embeddings und lokales LLM |
| PyMuPDF | PDF-Verarbeitung und -Lesen |
| Jupyter Notebook | Entwicklungs- und Experimentierumgebung |
| Ollama | Lokale LLM-Laufzeit |
| FAISS | Schnelle Vektordatenbank |

---

## 📁 Projektstruktur

```
RAG-test/
│
├── data/
│   ├── text_files/          # Textdokumente (TXT)
│   └── pdf/                 # PDF-Dokumente (z. B. doctorfrankel.pdf)
│
├── document.ipynb           # Notebook zur Datenerfassung (Data Ingestion)
├── pdf_loader.ipynb         # Notebook der vollständigen RAG-Pipeline
├── requirements.txt         # Python-Abhängigkeiten
└── README.md
```

---

## ⚙️ Installation & Einrichtung

### 1. Repository klonen

```bash
git clone https://github.com/your-username/rag-pipeline.git
cd rag-pipeline
```

### 2. Virtuelle Umgebung erstellen

```bash
python -m venv .venv

# Windows
.venv\Scripts\activate

# macOS / Linux
source .venv/bin/activate
```

### 3. Abhängigkeiten installieren

```bash
pip install -U langchain langchain-community langchain-ollama pymupdf jupyter faiss-cpu
```

Oder, falls eine requirements.txt vorhanden ist:

```bash
pip install -r requirements.txt
```

### 4. Ollama installieren & starten

Laden Sie zunächst Ollama von der offiziellen Website herunter und installieren Sie es: https://ollama.com

Laden Sie anschließend die benötigten Modelle herunter:

```bash
ollama pull llama3
ollama pull nomic-embed-text
```

Starten Sie schließlich den Ollama-Dienst:

```bash
ollama serve
```

---

## 🚀 Verwendung

### 1. Daten vorbereiten

Legen Sie Ihre PDF-Dateien im Ordner data/pdf/ und Ihre Textdateien im Ordner data/text_files/ ab.

Beispiel: Legen Sie das Buch doctorfrankel.pdf („… trotzdem Ja zum Leben sagen") in den Ordner data/pdf/.

### 2. Dokumente laden

```python
from langchain_community.document_loaders import PyMuPDFLoader, DirectoryLoader

loader = DirectoryLoader(
    "../data/pdf",
    glob="**/*.pdf",
    loader_cls=PyMuPDFLoader,
)
documents = loader.load()
print(f"Anzahl geladener Dokumente: {len(documents)}")
```

### 3. Dokumente in kleinere Abschnitte aufteilen

```python
from langchain_classic.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    length_function=len,
    separators=["\n\n", "\n", " ", ""]
)
chunks = splitter.split_documents(documents)
print(f"Anzahl der Abschnitte: {len(chunks)}")
```

### 4. Einbetten und in einer Vektordatenbank speichern

```python
from langchain_ollama import OllamaEmbeddings
from langchain_community.vectorstores import FAISS

embeddings = OllamaEmbeddings(model="nomic-embed-text")
vectorstore = FAISS.from_documents(chunks, embeddings)
vectorstore.save_local("faiss_index")
```

### 5. Das LLM abfragen (vollständiges RAG)

```python
from langchain_ollama import ChatOllama

llm = ChatOllama(model="llama3")
retriever = vectorstore.as_retriever(search_kwargs={"k": 4})

query = "Was ist Logotherapie und was sagt Frankl über den Sinn des Lebens?"
docs = retriever.invoke(query)
context = "\n\n".join(d.page_content for d in docs)

prompt = f"""Beantworte die Frage anhand des folgenden Textes:

Text:
{context}

Frage: {query}

Antwort:"""

answer = llm.invoke(prompt)
print(answer.content)
```

---

## 🧠 Praxisbeispiel zu Daten der psychischen Gesundheit

Dieses Projekt arbeitet standardmäßig mit Büchern aus den Bereichen Psychologie und psychische Gesundheit. Sie können beispielsweise folgende Fragen stellen:

- „Wie erklärt Frankl den Sinn des Leidens?"
- „Welche Beziehung besteht zwischen Hoffnung und Überleben in Gefangenenlagern?"
- „Wie unterscheidet sich die Logotherapie von der Freudschen Psychoanalyse?"
- „Was bedeutet das Konzept der ‚letzten Freiheit des Menschen' in ‚… trotzdem Ja zum Leben sagen'?"

---

## 📌 Zukunftspläne

- [ ] Unterstützung für ChromaDB und Pinecone als alternative Vektordatenbanken hinzufügen
- [ ] Eine Web-Oberfläche mit Streamlit oder Gradio erstellen
- [ ] Streaming-Antworten für ein besseres Nutzererlebnis hinzufügen
- [ ] Das Projekt für eine einfachere Bereitstellung dockerisieren
- [ ] RAG-Bewertungsmetriken hinzufügen (z. B. RAGAS)
- [ ] Unterstützung für Hybrid Search
- [ ] Konversationsgedächtnis (Conversation Memory) hinzufügen

---

## 🤝 Mitwirken

Beiträge zu diesem Projekt sind sehr willkommen! Um beizutragen:

1. Forken Sie das Repository
2. Erstellen Sie einen neuen Branch:
   ```bash
   git checkout -b feature/amazing-feature
   ```
3. Committen Sie Ihre Änderungen:
   ```bash
   git commit -m "Add amazing feature"
   ```
4. Pushen Sie auf Ihren Branch:
   ```bash
   git push origin feature/amazing-feature
   ```
5. Öffnen Sie einen Pull Request

---

## 📜 Lizenz

Dieses Projekt wird unter der **MIT-Lizenz** veröffentlicht. Der vollständige Lizenztext ist unten angegeben:

```
MIT License
```
