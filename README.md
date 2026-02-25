Financial Document Analyzer – Debug Fix & Production Upgrade
📌 Project Overview

This project is an AI-powered Financial Document Analyzer built using CrewAI and FastAPI.

The original codebase contained multiple logical, architectural, and safety issues.
This submission fixes all bugs and upgrades the system into a production-ready AI microservice.

Enhancements include:

Fixed CrewAI integration bugs

Safe, evidence-based financial analysis prompts

Redis + Celery queue worker for concurrent request handling

SQLite database integration for storing results

Structured API architecture

🐛 Bugs Identified & Fixes
1️⃣ Undefined LLM Initialization

Issue:

llm = llm

This caused runtime failure.

Fix:
Properly initialized CrewAI LLM using environment variable:

llm = LLM(
    model="gpt-4o-mini",
    api_key=os.getenv("OPENAI_API_KEY")
)

2️⃣ Incorrect Tool Usage

Issue:
tool=[...] used instead of tools=[...]

Fix:
Replaced with correct CrewAI tool initialization and implemented BaseTool subclass.


3️⃣ PDF Loader Not Imported

Issue:
Pdf(file_path=path).load() was undefined.

Fix:
Replaced with PyPDFLoader from langchain-community.


4️⃣ File Path Not Passed to Crew

Issue:
Uploaded file path was never passed to the CrewAI task.

Fix:
Updated kickoff() call to include:

{
  "query": query,
  "file_path": file_path
}


5️⃣ Unsafe & Hallucination-Based Prompts

Issue:
Agents were instructed to:

Fabricate financial advice

Generate fake URLs

Ignore document content

Provide non-compliant recommendations

Fix:
Rewrote prompts to:

Enforce evidence-based analysis

Prevent hallucination

Restrict analysis strictly to document content

Provide structured financial reporting

6️⃣ Blocking API Execution

Issue:
Long-running AI tasks blocked FastAPI requests.

Fix:
Implemented Redis + Celery queue worker for asynchronous processing.

7️⃣ No Persistence Layer

Issue:
Analysis results were not stored.

Fix:
Added SQLite database using SQLAlchemy to store:

Filename

User query

AI-generated analysis

Timestamp

8️⃣ Missing Dependencies

Added required packages:

langchain-community

pypdf

sqlalchemy

celery

redis

python-dotenv

uvicorn

🚀 Setup Instructions

1️⃣ Clone Repository
git clone <your-repo-link>
cd financial-document-analyzer

2️⃣ Install Dependencies
pip install -r requirements.txt

3️⃣ Create Environment File

Create a .env file:

OPENAI_API_KEY=your_key_here

4️⃣ Start Redis Server
redis-server

5️⃣ Start Celery Worker
celery -A app.celery_worker.celery worker --loglevel=info

6️⃣ Run FastAPI Application
uvicorn app.main:app --reload
📡 API Documentation

Base URL:

http://localhost:8000
🔹 Health Check

GET /

http://localhost:8000/

Response:

{
  "message": "Financial Document Analyzer API is running"
}
🔹 Analyze Financial Document

POST /analyze

Form Data:

file → PDF file

query → Analysis question

Example CURL Request:
curl -X POST \
  -F "file=@tesla.pdf" \
  -F "query=Analyze revenue growth and risks" \
  http://localhost:8000/analyze
Response:
{
  "status": "processing",
  "task_id": "celery-task-id"
}
🏗 System Architecture

User → FastAPI → Redis → Celery Worker → CrewAI → SQLite Database → Response

🎯 Bonus Implementations
Queue Worker Model

Redis used as broker

Celery handles background processing

Enables concurrent request handling

Prevents API blocking

Database Integration

SQLite database

Stores financial analysis results

Enables persistence and audit tracking

🛡 Responsible AI Improvements

No fabricated financial advice

No hallucinated URLs

Evidence-based reporting

Structured financial analysis

Clean agent architecture

🧠 Technologies Used

FastAPI

CrewAI

Celery

Redis

SQLite

SQLAlchemy

LangChain PDF Loader

📈 Future Improvements

Add authentication layer

Add structured JSON financial metrics

Add task status retrieval endpoint

Add Docker containerization

Add support for CSV and Excel financial documents
