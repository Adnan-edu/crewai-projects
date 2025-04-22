# CrewAI Job Interview Preparation Assistant

## Overview

This project utilizes the CrewAI framework to create an automated job interview preparation assistant. It helps users get ready for specific job interviews by:

1.  **Researching:** Gathering detailed information about the target company and the interviewer (if known).
2.  **Generating Questions:** Creating a list of tailored interview questions based on the job description, company information, and interviewer profile, covering both cultural and positional fit.
3.  **Practicing:** Simulating an interview experience where the user answers generated questions and receives AI-driven feedback on their responses.

The goal is to streamline the interview preparation process, providing relevant background information and a platform for practice and improvement.

## Features

- **Automated Research:** Leverages web scraping and search tools (Serper) to find recent news, developments, and professional background information on the company and interviewer.
- **Tailored Question Generation:** Uses an LLM (GPT-4o) to create relevant interview questions based on the specific job role, description, and researched context.
- **Information Export:** Saves the generated company report, interviewer report, and interview questions into separate `.txt` files for easy review.
- **Interactive Interview Practice:** Engages the user in a mock interview, asking generated questions one by one.
- **Constructive Feedback:** Provides structured feedback (positives and negatives) on the user's answers, facilitated by an AI coach agent.
- **Iterative Improvement:** Allows for potential iterative feedback cycles within the practice session (though the example shows one round).

## Workflow

The process implemented in the `CrewAI_Job_Interview_Prep_Part_2.ipynb` notebook follows these main stages:

1.  **User Input:** The user provides essential details: Interviewer's Name (or 'NONE'), Company Name, Job Position, and the full Job Description.
2.  **Initialization:** Sets up the environment, loads API keys, and initializes the OpenAI LLM (GPT-4o).
3.  **Research & Question Generation (First Crew - Sequential Process):**
    - A `Research Agent` uses tools like `SerperDevTool` and `ScrapeWebsiteTool` to gather data about the company and the interviewer.
    - Separate tasks are executed to produce reports on the company and the interviewer.
    - A `Coach Agent` takes the research reports as context and generates a list of 8 tailored interview questions (split between culture and position fit).
    - The outputs (company report, interviewer report, questions) are saved to `.txt` files (e.g., `Beat81_report.txt`, `Matthias Glöel_report.txt`, `Data Analytics Working Student_interview_questions.txt`).
4.  **Interview Practice (Second Crew - Hierarchical Process):**
    - The generated questions are loaded from the text file.
    - An `Interviewer Agent` selects and poses one random question from the list to the user.
    - The user provides their answer via text input (`human_input=True`).
    - The `Coach Agent` (acting as manager) evaluates the user's answer based on the question asked.
    - The `Coach Agent` provides structured feedback, highlighting positives and negatives, and suggests improvements.
    - The process potentially loops for more questions/feedback (facilitated by the Crew's memory and hierarchical structure).

_(A visual representation of this workflow is provided in the `Interview prep part 2.jpg` file within the repository.)_

## How to Use

**Prerequisites:**

- Python 3.9+
- OpenAI API Key
- Serper API Key (for the `SerperDevTool`)

**Setup:**

1.  **Clone the Repository:**
    ```bash
    git clone <repository_url>
    cd <repository_directory>
    ```
2.  **Create a Virtual Environment:**
    ```bash
    python -m venv crewai_venv
    source crewai_venv/bin/activate # On Windows use `crewai_venv\Scripts\activate`
    ```
3.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```
    _(Note: If a `requirements.txt` is not provided, install the packages listed under Dependencies below)._
4.  **Set up Environment Variables:**
    - Create a `.env` file in the root directory.
    - Add your API keys to the `.env` file:
      ```env
      OPENAI_API_KEY="your_openai_api_key"
      SERPER_API_KEY="your_serper_api_key"
      # Optionally set OPENAI_MODEL_NAME if you want to use a specific model
      # OPENAI_MODEL_NAME="gpt-4o"
      ```

**Running the Notebook:**

1.  Launch Jupyter Notebook or Jupyter Lab:
    ```bash
    jupyter notebook CrewAI_Job_Interview_Prep_Part_2.ipynb
    # or
    jupyter lab CrewAI_Job_Interview_Prep_Part_2.ipynb
    ```
2.  Execute the cells sequentially from top to bottom.
3.  Provide the required inputs (Interviewer Name, Company, Position, Job Description) when prompted in the "User Inputs" section.
4.  Observe the research and question generation process. The results will be saved as `.txt` files.
5.  Engage with the interactive interview practice section:
    - An AI agent will ask you a question.
    - Provide your answer in the input field that appears (`human_input=True`).
    - An AI coach will provide feedback on your answer. Confirm the feedback to potentially continue the loop (depending on CrewAI setup).

## Core Components (CrewAI)

- **Research Agent:** Responsible for conducting web searches and scraping websites to gather information about the company and interviewer. Uses `SerperDevTool` and `ScrapeWebsiteTool`.
- **Coach Agent:** Acts as an interview coach. Responsible for generating interview questions based on research and providing structured feedback on user answers during the practice session. Also serves as the manager agent in the practice crew.
- **Interviewer Agent:** Simulates the interviewer during the practice session, posing questions to the user. Its persona can be influenced by the researched interviewer's details.

## Dependencies

- `crewai`: The core framework for orchestrating AI agents.
- `crewai_tools`: Provides pre-built tools like `SerperDevTool` and `ScrapeWebsiteTool`.
- `langchain-openai`: For interacting with OpenAI's language models.
- `openai`: OpenAI Python client library.
- `python-dotenv`: For managing environment variables (API keys).
- `ipython`: Used for running the code within a Jupyter Notebook environment.
- `requests`: Underlying library for web requests used by tools.
- `beautifulsoup4`: Often a dependency for scraping tools.
- `duckduckgo-search`: May be used by default search tools if Serper isn't configured/available.

_(Ensure these are installed, preferably via a `requirements.txt` file.)_
