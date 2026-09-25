text
[🇬🇧 English](#english) | [🇮🇷 فارسی](#فارسی) | [🇩🇪 Deutsch](#deutsch)
Good.

Let's draft.

markdown
[🇬🇧 English](#english) | [🇮🇷 فارسی](#فارسی) | [🇩🇪 Deutsch](#deutsch)

---

## 🇮🇷 فارسی

### 📌 معرفی

این پروژه یک **پایپ‌لاین کامل RAG (تولید تقویت‌شده با بازیابی)** را نشان می‌دهد که:

1. **بارگذاری** اسناد از فایل‌های PDF و TXT
2. **تقسیم** اسناد به قطعات کوچک‌تر با استفاده از `RecursiveCharacterTextSplitter`
3. **تبدیل** آن‌ها به بردار با `OllamaEmbeddings`
4. **ذخیره** در پایگاه داده برداری
5. **بازیابی** قطعات مرتبط و تولید پاسخ با `ChatOllama`

این پروژه با **LangChain** ساخته شده و از **اسناد چندزبانه** (انگلیسی، فارسی، آلمانی) پشتیبانی می‌کند.

---

### ✨ ویژگی‌ها

- 📄 بارگذارنده‌های PDF و TXT (`PyMuPDFLoader`، `TextLoader`، `DirectoryLoader`)
- ✂️ تقسیم متن بازگشتی با اندازه و هم‌پوشانی قابل تنظیم
- 🔢 بردارسازی با Ollama
- 🗂️ یکپارچه‌سازی با پایگاه داده برداری
- 💬 پرسش و پاسخ مبتنی بر چت با مدل‌های زبانی Ollama
- 🌍 پشتیبانی از اسناد چندزبانه (EN / FA / DE)
- 📓 مبتنی بر Jupyter Notebook

---

### 🛠️ تکنولوژی‌ها

| ابزار | کاربرد |
|------|---------|
| Python 3.11+ | زبان اصلی |
| LangChain | هماهنگی RAG |
| LangChain Community | بارگذارنده‌های اسناد |
| LangChain Ollama | بردارسازی و مدل زبانی |
| PyMuPDF | تجزیه PDF |
| Jupyter Notebook | محیط توسعه |
| Ollama | اجرای مدل زبانی محلی |

---

### 📁 ساختار پروژه
RAG-test/
│
├── data/
│ ├── text_files/ # اسناد متنی
│ └── pdf/ # اسناد PDF
│
├── document.ipynb # نوت‌بوک ورود داده
├── pdf_loader.ipynb # نوت‌بوک پایپ‌لاین RAG
├── requirements.txt # وابستگی‌ها
└── README.md

text

---

### ⚙️ نصب و راه‌اندازی

#### ۱. کلون کردن مخزن

```bash
git clone https://github.com/your-username/rag-pipeline.git
cd rag-pipeline
۲. ساخت محیط مجازی
bash
python -m venv .venv

# ویندوز
.venv\Scripts\activate

# مک / لینوکس
source .venv/bin/activate
۳. نصب وابستگی‌ها
bash
pip install -U langchain langchain-community langchain-ollama pymupdf jupyter faiss-cpu
یا:

bash
pip install -r requirements.txt
۴. نصب و اجرای Ollama
از https://ollama.com دانلود کنید و سپس:

bash
ollama pull llama3
ollama pull nomic-embed-text
ollama serve
🚀 استفاده
۱. آماده‌سازی داده‌ها
فایل‌های PDF را در data/pdf/ و فایل‌های متنی را در data/text_files/ قرار دهید.

۲. بارگذاری اسناد
python
from langchain_community.document_loaders import PyMuPDFLoader, DirectoryLoader

loader = DirectoryLoader(
    "../data/pdf",
    glob="**/*.pdf",
    loader_cls=PyMuPDFLoader,
)
documents = loader.load()
۳. تقسیم اسناد
python
from langchain_classic.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
)
chunks = splitter.split_documents(documents)
۴. بردارسازی و ذخیره‌سازی
python
from langchain_ollama import OllamaEmbeddings
from langchain_community.vectorstores import FAISS

embeddings = OllamaEmbeddings(model="nomic-embed-text")
vectorstore = FAISS.from_documents(chunks, embeddings)
۵. پرسش از مدل زبانی
python
from langchain_ollama import ChatOllama

llm = ChatOllama(model="llama3")
retriever = vectorstore.as_retriever()

query = "لوگوتراپی چیست؟"
docs = retriever.invoke(query)
context = "\n\n".join(d.page_content for d in docs)

answer = llm.invoke(f"بر اساس متن زیر پاسخ بده:\n{context}\n\nسوال: {query}")
print(answer.content)
📌 برنامه‌های آینده
□ افزودن پشتیبانی از ChromaDB و Pinecone
□ ساخت رابط کاربری با Streamlit یا Gradio
□ افزودن پاسخ‌های استریم
□ داکرایز کردن پروژه
□ افزودن معیارهای ارزیابی (RAGAS)
🤝 مشارکت
مخزن را Fork کنید

شاخه بسازید: git checkout -b feature/amazing-feature

کامیت کنید: git commit -m "Add amazing feature"

پوش کنید: git push origin feature/amazing-feature

یک Pull Request باز کنید

📜 مجوز
تحت مجوز MIT منتشر شده است — برای جزئیات فایل LICENSE را ببینید.

⭐ حمایت
اگر این پروژه برایتان مفید بود، لطفاً یک ستاره ⭐ در GitHub به آن بدهید!

ساخته شده با ❤️ با استفاده از LangChain و Ollama