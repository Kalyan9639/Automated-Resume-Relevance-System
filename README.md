# AI-Powered Resume Ranking System (V4)

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/Backend-FastAPI-green)](https://fastapi.tiangolo.com/)
[![Streamlit](https://img.shields.io/badge/Frontend-Streamlit-red)](https://streamlit.io/)
[![LangChain](https://img.shields.io/badge/Framework-LangChain-purple)](https://www.langchain.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An intelligent, multi-layered resume screening and ranking system designed to automate and enhance the recruitment process. This project uses a modern AI stack, including LangGraph for workflow orchestration, sentence-transformer embeddings for semantic search, and a Large Language Model (LLM) for candidate analysis.

---

## 🌟 Live Demo Preview


---

## 🎯 The Problem

In a competitive job market, recruitment teams are inundated with thousands of applications for each job opening. Manually screening resumes against job descriptions (JDs) is a significant bottleneck, leading to:

* **Time Delays:** Slowing down the entire hiring pipeline.
* **Inconsistency:** Human evaluators may interpret requirements differently, leading to biased or inconsistent shortlisting.
* **High Workload:** Overburdening placement staff and recruiters, preventing them from focusing on higher-value tasks like candidate engagement and interview preparation.
* **Missed Opportunities:** Qualified candidates can be overlooked due to human error or fatigue.

This project directly addresses these challenges by creating a robust, scalable, and consistent automated system for resume evaluation.

---

## ✨ Key Features

This application provides a comprehensive solution for modern, data-driven recruitment:

* **Multi-File Upload:** Accepts resumes in various formats (`.pdf`, `.docx`, `.txt`).
* **Flexible Job Description Input:** Recruiters can either paste the JD text directly or upload a JD file (`.pdf`).
* **Two-Layer Ranking Engine:**
    1.  **Semantic Search:** Utilizes `all-mpnet-base-v2` sentence-transformer embeddings to find resumes that are contextually and conceptually similar to the job description, going beyond simple keyword matching.
    2.  **Keyword Skill Re-ranking:** Re-ranks the semantically similar candidates based on the presence of specific, required skills for a more precise final ordering.
* **AI-Powered Verdict & Feedback:** Leverages the high-speed Groq API with the `gemma2-9b-it` model to provide:
    * A concise, actionable **AI Verdict** (e.g., "Strong Fit", "Potential Fit", "Lacks Key Skills").
    * **Constructive Feedback** explaining the verdict based on the resume's content.
* **Interactive Dashboard:** A sleek and modern frontend built with Streamlit, featuring:
    * Detailed metrics on processing time and candidates analyzed.
    * Visual skill-match indicators (progress circles, matched/missing skill badges).
    * Clear presentation of key matching evidence from the resume.
* **Data Export:** Allows exporting the final ranked list to a `.csv` file for reporting and offline analysis.
* **Scalable Backend:** Built with FastAPI and orchestrated by LangGraph, ensuring a robust, stateful, and easily maintainable workflow that is ready for cloud deployment.

---

## 🏗️ System Architecture

The system is designed with a decoupled frontend and backend architecture for scalability and maintainability.


1.  **Frontend (Streamlit):**
    * The user interacts with the Streamlit dashboard to input the job title, job description (text or PDF), required skills, and resumes.
    * It handles file uploads and sends a `POST` request to the FastAPI backend.
    * Once the analysis is complete, it receives the JSON response and renders the results in a user-friendly, interactive format.

2.  **Backend (FastAPI & LangGraph):**
    * A single API endpoint (`/rank-resumes`) receives the job details and resume files.
    * The core logic is orchestrated by a **LangGraph** state machine, which ensures a clear, step-by-step process.

### LangGraph Workflow

The agentic workflow proceeds through the following nodes:

1.  **`preprocess_resumes`:**
    * Extracts raw text from each uploaded resume (`.pdf`, `.docx`, `.txt`).
    * Cleans the text and attempts to identify key sections like 'Experience' and 'Skills'.
    * Creates LangChain `Document` objects for each relevant section or the entire resume.

2.  **`semantic_search` (Layer 1 Ranking):**
    * Generates embeddings for all resume document chunks using `SentenceTransformerEmbeddings`.
    * Creates an in-memory `Chroma` vector store for efficient similarity searching.
    * Queries the vector store with the job description to find the most semantically relevant resume chunks.
    * This step identifies a pool of top candidates based on contextual relevance.

3.  **`skill_rerank` (Layer 2 Ranking):**
    * Takes the semantically matched candidates and performs a keyword search for the `required_skills`.
    * Calculates the number of matched vs. missing skills for each candidate.
    * Re-ranks the list, prioritizing candidates with the highest skill match count, followed by their semantic score.

4.  **`generate_feedback` (AI Analysis):**
    * For the final top-ranked candidates, this node makes an asynchronous call to the Groq API.
    * It prompts the LLM with the candidate's experience/skills sections and the required skills.
    * The LLM returns a structured JSON object containing the `ai_verdict` and `ai_feedback`.

5.  **END:** The final state, containing the fully ranked and analyzed candidate list, is returned as a JSON response to the frontend.

---

## 🛠️ Tech Stack

| Category              | Technology / Library                                                                                   | Purpose                                                                |
| --------------------- | ------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------- |
| **Backend** | `FastAPI`, `Uvicorn`                                                                                   | High-performance asynchronous API framework and server.                |
| **Frontend** | `Streamlit`                                                                                            | Rapidly creating and deploying the interactive web dashboard.          |
| **AI Orchestration** | `LangGraph`                                                                                            | Building robust, stateful, multi-step agentic workflows.               |
| **LLM & Embeddings** | `langchain-groq`, `langchain-community`                                                                | Interfacing with Groq's LLM and using Sentence-Transformer embeddings. |
| **Vector Store** | `ChromaDB`                                                                                             | In-memory vector database for efficient semantic search.               |
| **File Parsing** | `PyPDF2`, `python-docx`, `PDFMinerLoader`                                                              | Extracting text from `.pdf` and `.docx` files.                         |
| **Core Language** | `Python 3.9+`                                                                                          | The primary programming language for the entire stack.                 |

---

## 🚀 Getting Started

Follow these instructions to set up and run the project locally.

### Prerequisites

* Python 3.9 or higher
* `pip` and `venv` for package management

### 1. Clone the Repository

```bash
git clone [https://github.com/your-username/your-repo-name.git](https://github.com/your-username/your-repo-name.git)
cd your-repo-name
```

### 2. Set Up a Virtual Environment

It's highly recommended to use a virtual environment to manage dependencies.
```bash
# For Windows
python -m venv venv
venv\Scripts\activate

# For macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
Create a requirements.txt file with the following content:

```
fastapi
uvicorn[standard]
streamlit
requests
pandas
pydantic
python-docx
PyPDF2
langchain
langgraph
langchain-core
langchain-groq
langchain-chroma
langchain-community
sentence-transformers
pdfminer.six
chromadb
```

Now, install all the required packages:

```bash
pip install -r requirements.txt
```

**Note:** The first time you run the application, sentence-transformers will download the all-mpnet-base-v2 model (approx. 420MB).

### 4. Running the Application
You need to run the backend and frontend in two separate terminals.

***Terminal 1: Start the FastAPI Backend***

Navigate to the project directory and run:

```bash
uvicorn final_main:app --reload
```

The backend API will now be running at http://127.0.0.1:8000.

***Terminal 2: Start the Streamlit Frontend***

In a new terminal, navigate to the same project directory and run:

```bash
streamlit run st_main_6.py
```

The Streamlit web application will open in your browser, usually at http://localhost:8501.

### 5. Using the Application
Open the Streamlit app in your browser.

Fill in the "Job Details" and "Required Skills" in the sidebar.

Select a valid Groq API Key from the dropdown. (You can add your own keys to the list in st_main_6.py).

Upload resume files and optionally a job description PDF.

Click the "🚀 Start Analysis" button and view the results.

---

## 🔌 API Endpoint
The backend provides the following endpoint for programmatic access:

POST /rank-resumes
This endpoint accepts multipart/form-data and performs the complete resume ranking workflow.

**Form Fields:**

* ```job_title``` (str): The title of the job role.

* ```job_description``` (str, optional): The full text of the job description.

* ```required_skills``` (str): A comma-separated string of required skills.

* ```groq_api_key``` (str, optional): Your Groq API key for generating AI feedback.

* ```top_n``` (int, optional): The number of top candidates to return. Defaults to 10.

* ```files``` (List[UploadFile]): The list of resume files to be processed.

* ```jd_file``` (UploadFile, optional): A single PDF file containing the job description. If provided, this will be used instead of the job_description text field.

---

## 📈 Future Improvements:

* Persistent Storage: Integrate a database like PostgreSQL or a cloud-based solution to store job postings, results, and candidate history.

* User Authentication: Add a login system for recruiters and placement teams.

* Advanced Scoring: Develop a more sophisticated weighted scoring mechanism that combines semantic score, skill match count, and other factors (e.g., years of experience) into a single "Relevance Score".

* Observability: Integrate LangSmith for better tracing, debugging, and monitoring of the LLM and LangGraph chains.

* Containerization: Dockerize the frontend and backend services for easier deployment and scaling using Docker Compose or Kubernetes.
