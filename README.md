AI Researcher

Your new AI Researcher, that can browse papers on a given topic at arxiv.org, read papers in depth via the read_pdf tool, perform research and export a ready-to-publish research paper. (ONLY FOR EDUCATIONAL PURPOSES)

🚀 Quick Start

Clone the repo and run:

Setup UV if not already:  https://www.youtube.com/watch?v=Dgf7Lp0B_hI

```
uv sync
```

That’s it. This command:

Creates a virtual environment (if one doesn't exist)

Installs all dependencies from uv.lock

Sets up everything exactly as expected


TO INSTALL TECTONIC: [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072                           
>> iex ((New-Object System.Net.WebClient).DownloadString('https://drop-ps1.fullyjustified.net'))

TO RUN APPLICATION: uv run streamlit run frontend.py

1. arXiv Search Tool – Overview

This code implements a custom **LangChain tool** that allows an AI agent to search arXiv for recently published research papers on a given topic. The `search_arxiv_papers()` function prepares the search query, validates it, constructs the arXiv API URL, sends an HTTP request, and retrieves the response in XML format.

The `parse_arxiv_xml()` function processes the XML response and extracts useful metadata from each paper, including the **title, abstract (summary), authors, research categories, and PDF link**. The extracted information is converted into a structured Python dictionary for easy consumption.

Finally, the `arxiv_search()` function is exposed as a LangChain tool using the `@tool` decorator. This allows an AI agent to invoke it dynamically whenever it needs to fetch recent academic papers on a specific topic. In short, the code acts as a bridge between an AI agent and the arXiv research repository, converting raw XML responses into structured research data.


2. PDF Reader Tool – Overview

This code creates a custom LangChain tool that reads a PDF file from a given URL and extracts its text content. The read_pdf() function first downloads the PDF using the requests library and loads it into memory using BytesIO, so no local file storage is required.

The downloaded PDF is then processed using PyPDF2, which reads the document page by page. The function loops through all pages, extracts the text from each page using extract_text(), and combines the text into a single string. Progress messages are printed during extraction, showing which page is currently being processed.

Finally, the extracted text is returned to the caller. If any error occurs during download or parsing (such as an invalid URL, corrupted PDF, or network issue), the exception is logged and re-raised. By using the @tool decorator, the function becomes available to a LangChain agent, allowing the agent to automatically read and analyze PDF documents from URLs.


3. Write pdf – Overview

This code creates a custom LangChain tool that converts LaTeX content into a PDF document using the Tectonic LaTeX engine. The tool receives a complete LaTeX document as input, compiles it, and returns the path of the generated PDF file.

The function first checks whether Tectonic is installed on the system. If it is not available, it immediately throws an error because PDF generation depends on it. It then creates an output directory (if it does not already exist) and generates unique .tex and .pdf filenames using a timestamp to avoid overwriting previous files.

Next, the LaTeX content is written into a temporary .tex file. The code invokes the Tectonic compiler using subprocess.run(), which compiles the LaTeX source into a PDF. After compilation, it verifies that the PDF was successfully generated. If the PDF exists, its file path is returned; otherwise, an error is raised.

Since the function is decorated with @tool, it can be called directly by a LangChain agent whenever a generated report, research paper, or formatted document needs to be converted into a professional PDF.


4. Research Agent Workflow – Overview

This code builds a **LangGraph-based AI research agent** that can search research papers from arXiv, read PDFs, analyze them, generate a new research paper, and finally export it as a PDF. It combines an LLM (Gemini), custom tools, and a LangGraph workflow to create an autonomous research assistant.

### Main Components

**1. State Management**

* Defines a `State` object that stores the conversation history (`messages`).
* Uses `add_messages` so new messages are automatically appended as the workflow progresses.

**2. Tool Integration**
The agent is given three tools:

* **arxiv_search** → Searches arXiv for relevant research papers.
* **read_pdf** → Downloads and extracts text from paper PDFs.
* **render_latex_pdf** → Converts generated LaTeX research papers into PDF documents.

These tools are wrapped inside a `ToolNode`, allowing the graph to execute them whenever the LLM requests a tool call.

**3. LLM Setup**

* Uses Gemini 2.5 Flash as the reasoning engine.
* Binds all tools to the model so it can decide when to invoke them.
* The LLM acts as the "brain" while tools perform external actions.

**4. Graph Workflow**
The graph contains two nodes:


User Input
    ↓
 Agent (LLM)
    ↓
Needs Tool?
 ├── Yes → Tools → Agent
 └── No  → End


* `call_model()` sends messages to Gemini.
* `should_continue()` checks whether Gemini requested any tool calls.
* If tool calls exist, execution moves to the ToolNode.
* Tool results are returned to the LLM.
* The cycle repeats until no more tools are needed.

**5. Memory**

* `MemorySaver()` stores conversation history.
* The `thread_id` ensures the same conversation context is preserved across multiple interactions.
* This allows the agent to remember previously discussed papers and research ideas.

### Research Process Defined by the System Prompt

The prompt instructs the agent to:

1. Discuss and identify a research topic.
2. Search recent papers from arXiv.
3. Present relevant papers with PDF links.
4. Read selected papers.
5. Analyze findings and future research opportunities.
6. Suggest new research directions.
7. Generate a complete research paper.
8. Include mathematical equations.
9. Convert the final paper into LaTeX.
10. Export the LaTeX as a PDF.

### End-to-End Flow


User chooses topic
        ↓
Search arXiv papers
        ↓
Display recent papers
        ↓
User selects paper
        ↓
Read PDF content
        ↓
Analyze research & future work
        ↓
Generate new research ideas
        ↓
Write complete paper
        ↓
Create LaTeX document
        ↓
Render PDF
        ↓
Return PDF path


--In Short:

This code implements a LangGraph-based autonomous research agent using Gemini and three custom tools (arXiv search, PDF reader, and LaTeX PDF generator). The graph repeatedly lets the LLM reason, call tools when needed, and continue processing until the task is complete. The agent can search academic papers, read and analyze them, propose new research directions, generate a full research paper with equations, and export the final result as a PDF.
