# 🏥 End-to-End Medical Chatbot

An intelligent Medical Chatbot powered by **Retrieval-Augmented Generation (RAG)** that answers health-related queries using a knowledge base built from medical PDF documents.

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![Flask](https://img.shields.io/badge/Flask-3.1.1-green.svg)
![LangChain](https://img.shields.io/badge/LangChain-0.3.26-orange.svg)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o-purple.svg)
![AWS](https://img.shields.io/badge/AWS-EC2%20|%20ECR-yellow.svg)
![Docker](https://img.shields.io/badge/Docker-Containerized-blue.svg)

---

## 📌 Table of Contents

- [Features](#-features)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Installation](#-installation)
- [Usage](#-usage)
- [Docker Deployment](#-docker-deployment)
- [AWS Deployment](#-aws-deployment)
- [CI/CD Pipeline](#-cicd-pipeline)
- [API Endpoints](#-api-endpoints)
- [Contributing](#-contributing)
- [License](#-license)

---

## ✨ Features

- **🔍 RAG-based Q&A**: Retrieves relevant context from medical knowledge base to provide accurate answers
- **📄 PDF Knowledge Base**: Automatically processes and indexes medical PDF documents
- **⚡ Vector Search**: Uses Pinecone for fast and scalable similarity search
- **🤖 GPT-4o Integration**: Leverages OpenAI's latest model for high-quality responses
- **💬 Modern Chat UI**: Clean, responsive interface built with HTML/CSS/JavaScript
- **🐳 Dockerized**: Fully containerized for easy deployment
- **🚀 CI/CD Pipeline**: Automated deployment to AWS using GitHub Actions

---

## 🏗 Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   User Query    │────▶│  Flask Server   │────▶│    Pinecone     │
│                 │     │                 │     │  Vector Store   │
└─────────────────┘     └────────┬────────┘     └────────┬────────┘
                                 │                       │
                                 ▼                       │
                        ┌─────────────────┐              │
                        │     GPT-4o      │◀─────────────┘
                        │   (LangChain)   │   Retrieved Context
                        └────────┬────────┘
                                 │
                                 ▼
                        ┌─────────────────┐
                        │    Response     │
                        └─────────────────┘
```

### Data Flow:
1. **PDF Loading**: Medical PDFs are loaded from the `data/` directory
2. **Text Splitting**: Documents are split into 500-character chunks with 20-character overlap
3. **Embedding**: Text chunks are converted to 384-dimensional vectors using HuggingFace
4. **Indexing**: Vectors are stored in Pinecone for fast retrieval
5. **Query Processing**: User queries are embedded and matched against stored vectors
6. **Response Generation**: GPT-4o generates answers using retrieved context

---

## 🛠 Tech Stack

| Category | Technology |
|----------|------------|
| **Language** | Python 3.10+ |
| **Framework** | Flask |
| **LLM Orchestration** | LangChain |
| **LLM** | OpenAI GPT-4o |
| **Vector Database** | Pinecone |
| **Embeddings** | HuggingFace (all-MiniLM-L6-v2) |
| **Containerization** | Docker |
| **Cloud** | AWS (EC2, ECR) |
| **CI/CD** | GitHub Actions |

---

## 📁 Project Structure

```
End-to-End-Medical-Chatbot/
├── .github/
│   └── workflows/
│       └── cicd.yaml          # CI/CD pipeline configuration
├── data/
│   └── Medical_book.pdf       # Medical knowledge base (PDF)
├── src/
│   ├── __init__.py
│   ├── helper.py              # PDF loading, text splitting, embeddings
│   └── prompt.py              # System prompt configuration
├── static/
│   ├── css/
│   │   └── style.css          # Chat UI styles
│   └── js/
│       └── script.js          # Frontend JavaScript
├── templates/
│   └── chat.html              # Chat interface template
├── app.py                     # Main Flask application
├── store_index.py             # Script to index PDFs into Pinecone
├── Dockerfile                 # Docker configuration
├── requirements.txt           # Python dependencies
├── setup.py                   # Package setup
├── .env                       # Environment variables (not in repo)
├── .gitignore
├── LICENSE
└── README.md
```

---

## 🚀 Installation

### Prerequisites

- Python 3.10+
- OpenAI API Key
- Pinecone API Key
- Git

### Step 1: Clone the Repository

```bash
git clone https://github.com/manola1109/End-to-End-Medical-Chatbot.git
cd End-to-End-Medical-Chatbot
```

### Step 2: Create Virtual Environment

```bash
# Windows
python -m venv medibot
.\medibot\Scripts\Activate.ps1

# Linux/Mac
python3 -m venv medibot
source medibot/bin/activate
```

### Step 3: Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 4: Configure Environment Variables

Create a `.env` file in the root directory:

```env
PINECONE_API_KEY=your_pinecone_api_key
OPENAI_API_KEY=your_openai_api_key
```

### Step 5: Add Medical PDF

Place your medical PDF document(s) in the `data/` folder.

### Step 6: Index Documents

```bash
python store_index.py
```

### Step 7: Run the Application

```bash
python app.py
```

Visit `http://localhost:8080` in your browser.

---

## 💻 Usage

1. Open the chatbot in your browser at `http://localhost:8080`
2. Type your medical question in the input field
3. Press Enter or click Send
4. The chatbot will retrieve relevant information and provide an answer

**Example Questions:**
- "What are the symptoms of diabetes?"
- "How is hypertension treated?"
- "What causes migraine headaches?"

---

## 🐳 Docker Deployment

### Build the Image

```bash
docker build -t medical-chatbot .
```

### Run the Container

```bash
docker run -d \
  -p 8080:8080 \
  -e PINECONE_API_KEY=your_key \
  -e OPENAI_API_KEY=your_key \
  --name medical-chatbot \
  medical-chatbot
```

### Using Docker Compose (Optional)

```yaml
version: '3.8'
services:
  medical-chatbot:
    build: .
    ports:
      - "8080:8080"
    environment:
      - PINECONE_API_KEY=${PINECONE_API_KEY}
      - OPENAI_API_KEY=${OPENAI_API_KEY}
```

---

## ☁️ AWS Deployment

### Prerequisites

- AWS Account
- EC2 Instance (Ubuntu)
- ECR Repository
- IAM User with ECR and EC2 permissions

### ECR Repository

```
388299168736.dkr.ecr.ap-south-1.amazonaws.com/medicalchatbot
```

### EC2 Setup

1. Launch an Ubuntu EC2 instance
2. Install Docker:
   ```bash
   sudo apt update
   sudo apt install docker.io -y
   sudo usermod -aG docker ubuntu
   ```
3. Configure GitHub Actions Runner (self-hosted)

---

## 🔄 CI/CD Pipeline

The project uses GitHub Actions for automated deployment.

### Workflow: `.github/workflows/cicd.yaml`

| Job | Description |
|-----|-------------|
| **Continuous-Integration** | Builds Docker image and pushes to AWS ECR |
| **Continuous-Deployment** | Pulls image on EC2 and runs the container |

### Required GitHub Secrets

| Secret | Description |
|--------|-------------|
| `AWS_ACCESS_KEY_ID` | AWS IAM Access Key |
| `AWS_SECRET_ACCESS_KEY` | AWS IAM Secret Key |
| `AWS_DEFAULT_REGION` | `ap-south-1` |
| `ECR_REPO` | `medicalchatbot` |
| `PINECONE_API_KEY` | Pinecone API Key |
| `OPENAI_API_KEY` | OpenAI API Key |

### Trigger Deployment

Push to `main` branch automatically triggers the pipeline:

```bash
git add .
git commit -m "your message"
git push origin main
```

---

## 📡 API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/` | GET | Chat interface |
| `/get` | POST | Send message and get response |
| `/health` | GET | Health check endpoint |

### Example API Call

```bash
curl -X POST http://localhost:8080/get \
  -d "msg=What are the symptoms of diabetes?"
```

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## ⚠️ Disclaimer

This chatbot is for **informational purposes only** and should not replace professional medical advice. Always consult a healthcare professional for medical concerns.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👨‍💻 Author

**Deepak Manola**
- GitHub: [@manola1109](https://github.com/manola1109)

---

## ⭐ Support

If you found this project helpful, please give it a ⭐ on GitHub!
