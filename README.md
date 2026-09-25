# 🧠 پروژه RAG — از ورود داده تا پایگاه داده برداری

> یک **پایپ‌لاین کامل RAG (تولید تقویت‌شده با بازیابی)** برای بارگذاری، تقسیم، بردارسازی و پرسش از اسناد (PDF و TXT) با استفاده از **LangChain** و **Ollama**.

---

## 📌 معرفی

این پروژه یک **پایپ‌لاین کامل RAG** را پیاده‌سازی می‌کند که به شما امکان می‌دهد روی مجموعه‌ای از اسناد متنی و PDF (مانند کتاب‌های روانشناسی و سلامت روان مثل «انسان در جستجوی معنا» نوشته ویکتور فرانکل) پرسش‌های هوشمند بپرسید.

این پایپ‌لاین شامل مراحل زیر است:

1. **بارگذاری** اسناد از فایل‌های PDF و TXT
2. **تقسیم** اسناد به قطعات کوچک‌تر با استفاده از `RecursiveCharacterTextSplitter`
3. **تبدیل** قطعات به بردار با `OllamaEmbeddings`
4. **ذخیره** بردارها در پایگاه داده برداری
5. **بازیابی** قطعات مرتبط و تولید پاسخ با `ChatOllama`

پروژه با **LangChain** ساخته شده و از **اسناد چندزبانه** (انگلیسی، فارسی، آلمانی) پشتیبانی می‌کند.

---

## ✨ ویژگی‌ها

- 📄 **بارگذارنده‌های اسناد**: `PyMuPDFLoader` برای PDF، `TextLoader` برای TXT و `DirectoryLoader` برای بارگذاری پوشه‌ای
- ✂️ **تقسیم متن بازگشتی** با اندازه و هم‌پوشانی قابل تنظیم
- 🔢 **بردارسازی با Ollama** (مدل `nomic-embed-text`)
- 🗂️ **یکپارچه‌سازی با پایگاه داده برداری** (مانند FAISS / Chroma)
- 💬 **پرسش و پاسخ مبتنی بر چت** با مدل‌های زبانی Ollama (مانند `llama3`)
- 🌍 **پشتیبانی از اسناد چندزبانه** (انگلیسی، فارسی، آلمانی)
- 📓 **مبتنی بر Jupyter Notebook** — مناسب برای توسعه و آزمایش
- 🧠 **مناسب برای داده‌های سلامت روان و متون روانشناسی**

---

## 🛠️ تکنولوژی‌های استفاده‌شده

| ابزار | کاربرد |
|------|---------|
| Python 3.11+ | زبان اصلی پروژه |
| LangChain | هماهنگی و مدیریت پایپ‌لاین RAG |
| LangChain Community | بارگذارنده‌های اسناد (PDF / TXT) |
| LangChain Ollama | بردارسازی و مدل زبانی محلی |
| PyMuPDF | تجزیه و خواندن فایل‌های PDF |
| Jupyter Notebook | محیط توسعه و آزمایش |
| Ollama | اجرای مدل‌های زبانی به‌صورت محلی |
| FAISS | پایگاه داده برداری سریع |

---

## 📁 ساختار پروژه
RAG-test/
│
├── data/
│ ├── text_files/ # اسناد متنی (TXT)
│ └── pdf/ # اسناد PDF (مانند doctorfrankel.pdf)
│
├── document.ipynb # نوت‌بوک ورود داده (Data Ingestion)
├── pdf_loader.ipynb # نوت‌بوک پایپ‌لاین کامل RAG
├── requirements.txt # وابستگی‌های پایتون
└── README.md

text

---

## ⚙️ نصب و راه‌اندازی

### ۱. کلون کردن مخزن

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
یا اگر فایل requirements.txt موجود باشد:

bash
pip install -r requirements.txt
۴. نصب و اجرای Ollama
ابتدا Ollama را از سایت رسمی دانلود و نصب کنید: https://ollama.com

سپس مدل‌های مورد نیاز را دانلود کنید:

bash
ollama pull llama3
ollama pull nomic-embed-text
و در نهایت سرویس Ollama را اجرا کنید:

bash
ollama serve
🚀 نحوه استفاده
۱. آماده‌سازی داده‌ها
فایل‌های PDF خود را در پوشه data/pdf/ و فایل‌های متنی را در پوشه data/text_files/ قرار دهید.

مثال: کتاب doctorfrankel.pdf (انسان در جستجوی معنا) را در پوشه data/pdf/ قرار دهید.

۲. بارگذاری اسناد
python
from langchain_community.document_loaders import PyMuPDFLoader, DirectoryLoader

loader = DirectoryLoader(
    "../data/pdf",
    glob="**/*.pdf",
    loader_cls=PyMuPDFLoader,
)
documents = loader.load()
print(f"تعداد اسناد بارگذاری‌شده: {len(documents)}")
۳. تقسیم اسناد به قطعات کوچک‌تر
python
from langchain_classic.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    length_function=len,
    separators=["\n\n", "\n", " ", ""]
)
chunks = splitter.split_documents(documents)
print(f"تعداد قطعات: {len(chunks)}")
۴. بردارسازی و ذخیره‌سازی در پایگاه داده برداری
python
from langchain_ollama import OllamaEmbeddings
from langchain_community.vectorstores import FAISS

embeddings = OllamaEmbeddings(model="nomic-embed-text")
vectorstore = FAISS.from_documents(chunks, embeddings)
vectorstore.save_local("faiss_index")
۵. پرسش از مدل زبانی (RAG کامل)
python
from langchain_ollama import ChatOllama

llm = ChatOllama(model="llama3")
retriever = vectorstore.as_retriever(search_kwargs={"k": 4})

query = "لوگوتراپی چیست و فرانکل چه نظری درباره معنای زندگی دارد؟"
docs = retriever.invoke(query)
context = "\n\n".join(d.page_content for d in docs)

prompt = f"""بر اساس متن زیر به سوال پاسخ بده:

متن:
{context}

سوال: {query}

پاسخ:"""

answer = llm.invoke(prompt)
print(answer.content)
🧠 مثال کاربردی روی داده‌های سلامت روان
این پروژه به‌طور پیش‌فرض با کتاب‌های روانشناسی و سلامت روان کار می‌کند. برای مثال، می‌توانید سوالاتی مانند موارد زیر بپرسید:

«فرانکل معنای رنج را چگونه توضیح می‌دهد؟»

«رابطه بین امید و زنده ماندن در اردوگاه‌های اسیران چیست؟»

«لوگوتراپی چه تفاوتی با روانکاوی فروید دارد؟»

«مفهوم "آزادی نهایی انسان" در کتاب انسان در جستجوی معنا چیست؟»

📌 برنامه‌های آینده
□ افزودن پشتیبانی از ChromaDB و Pinecone به‌عنوان پایگاه داده برداری جایگزین
□ ساخت رابط کاربری وب با Streamlit یا Gradio
□ افزودن پاسخ‌های استریم (Streaming) برای تجربه کاربری بهتر
□ داکرایز کردن پروژه برای اجرای آسان‌تر
□ افزودن معیارهای ارزیابی RAG (مانند RAGAS)
□ پشتیبانی از جستجوی ترکیبی (Hybrid Search)
□ افزودن حافظه مکالمه (Conversation Memory)
🤝 مشارکت
از مشارکت شما در این پروژه استقبال می‌شود! برای مشارکت:

مخزن را Fork کنید

یک شاخه جدید بسازید:

bash
git checkout -b feature/amazing-feature
تغییرات خود را کامیت کنید:

bash
git commit -m "Add amazing feature"
به شاخه خود پوش کنید:

bash
git push origin feature/amazing-feature
یک Pull Request باز کنید

📜 مجوز
این پروژه تحت مجوز MIT منتشر شده است. متن کامل مجوز در زیر آمده است:

text
MIT License

Copyright (c) 2025 [نام شما]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
