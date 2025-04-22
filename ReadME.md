# CrewAI Project Examples Collection

This document combines the README files for several distinct projects demonstrating the capabilities of the CrewAI framework. Each part showcases a different application of CrewAI, LangChain, and Large Language Models (like OpenAI's GPT models) for tasks ranging from interactive simulations to automated research, analysis, and data processing.

## Common Setup Notes

Before diving into the specific projects, here are some common setup requirements:

1.  **Python:** Ensure you have Python installed (typically 3.9+ is recommended for modern AI libraries).
2.  **Virtual Environment:** It is highly recommended to use a virtual environment for each project or for the collection as a whole to manage dependencies effectively.
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```
3.  **Core Dependencies:** Most of these projects share core dependencies. You'll generally need:
    ```bash
    pip install crewai langchain-openai python-dotenv jupyter
    ```
    Specific projects may require additional tools (like `crewai-tools`, `pandas`, specific search tool libraries). Please refer to the "Setup" or "Dependencies" section within each part for details. Creating and using a `requirements.txt` file is the best practice.
4.  **API Keys:** These projects require API keys for services like OpenAI and potentially Serper (for web search).
    - Create a `.env` file in the root directory of your project.
    - Add your keys to this file, for example:
      ```env
      OPENAI_API_KEY="your_openai_api_key_here"
      SERPER_API_KEY="your_serper_api_key_here" # Needed for Part 2 & 3
      # Optionally specify the model
      # OPENAI_MODEL_NAME="gpt-4o"
      ```
    - Ensure your `.env` file is included in your `.gitignore` to avoid committing secrets. The projects use `python-dotenv` to load these keys.
5.  **Jupyter Notebook:** The examples are provided as Jupyter Notebooks (`.ipynb` files) for interactive execution. Ensure you have Jupyter installed (`pip install jupyter`).

## Table of Contents

1.  [Part 1: CrewAI Job Interview Preparation Simulator](#part-1-crewai-job-interview-preparation-simulator)
    - [Overview (Part 1)](#overview-part-1)
    - [Features (Part 1)](#features-part-1)
    - [How it Works (Part 1)](#how-it-works-part-1)
    - [Technology Stack (Part 1)](#technology-stack-part-1)
    - [Setup (Part 1)](#setup-part-1)
    - [Usage (Part 1)](#usage-part-1)
    - [Input Data (`Data Analytics Working Student.docx`) (Part 1)](#input-data-generated_filetxt-part-1)
    - [Example Interaction Flow (Part 1)](#example-interaction-flow-part-1)
2.  [Part 2: CrewAI Job Interview Preparation Assistant](#part-2-crewai-job-interview-preparation-assistant)
    - [Overview (Part 2)](#overview-part-2)
    - [Features (Part 2)](#features-part-2)
    - [Workflow (Part 2)](#workflow-part-2)
    - [How to Use (Part 2)](#how-to-use-part-2)
    - [Core Components (CrewAI) (Part 2)](#core-components-crewai-part-2)
    - [Dependencies (Part 2)](#dependencies-part-2)
3.  [Part 3: CrewAI YouTube Partnership Scoring](#part-3-crewai-youtube-partnership-scoring)
    - [Overview/Description (Part 3)](#overviewdescription-part-3)
    - [Features (Part 3)](#features-part-3)
    - [Technology Stack (Part 3)](#technology-stack-part-3)
    - [How it Works (Part 3)](#how-it-works-part-3)
    - [Project Analysis (Part 3)](#project-analysis-part-3)
    - [Setup and Installation (Part 3)](#setup-and-installation-part-3)
    - [Usage (Part 3)](#usage-part-3)
    - [Output Example (Part 3)](#output-example-part-3)
4.  [Part 4: CrewAI PDF Invoice Processing Automation](#part-4-crewai-pdf-invoice-processing-automation)
    - [Overview (Part 4)](#overview-part-4)
    - [Features (Part 4)](#features-part-4)
    - [Workflow (Part 4)](#workflow-part-4)
    - [Components (Part 4)](#components-part-4)
    - [Setup and Installation (Part 4)](#setup-and-installation-part-4)
    - [Usage (Part 4)](#usage-part-4)
    - [Output Examples (Part 4)](#output-examples-part-4)
    - [Technologies Used (Part 4)](#technologies-used-part-4)

---

# Part 1: CrewAI Job Interview Preparation Simulator

## Overview (Part 1)

This project demonstrates how to use CrewAI, LangChain, and OpenAI's GPT models to simulate a job interview preparation session. It features two AI agents: an Interviewer who asks job-relevant questions and a Coach who provides feedback on the user's answers. The simulation is interactive, allowing the user to practice their responses in real-time.

## Features (Part 1)

- **AI Interviewer Agent:** Simulates a specific interviewer from a target company, asking questions relevant to a defined job position and description.
- **AI Coach Agent:** Evaluates the user's answer and provides structured feedback (Good, Bad, How to Improve).
- **Interactive Q&A:** Utilizes CrewAI's `human_input=True` feature to pause execution and wait for the user's typed answer.
- **Sequential Workflow:** Agents operate in a defined order: Interviewer asks -> User answers -> Coach gives feedback.
- **Contextual Feedback:** The Coach agent uses the context from the Interviewer's task (the question asked and the user's answer) to generate relevant feedback.
- **Basic Memory:** Implements a simple memory mechanism using `StringKnowledgeSource` to store previously asked questions, aiming to prevent the Interviewer from asking the exact same question again in subsequent interactions (within a potential iterative loop, though the notebook runs one cycle).

## How it Works (Part 1)

1.  **Initialization:**
    - Loads necessary environment variables (specifically `OPENAI_API_KEY`).
    - Defines the OpenAI LLM to be used (e.g., `gpt-4o`).
2.  **User Input:**
    - The script prompts the user to enter the Interviewer's name, Company name, Job Position, and Job Description using Python's `input()` function.
    - Alternatively, the ` Data Analytics Working Student.docx` provides example data that can be used as input or adapted into the code.
3.  **Agent Definition:**
    - **Interviewer Agent:** Configured with a specific `role` (incorporating the user-provided interviewer name, company, and position), a `goal` (to help the user prepare for the specific interview), and a `backstory` (currently a _hardcoded_ CV-like text, which could be made dynamic).
    - **Coach Agent:** Configured with the `role` of "Interview Coach", a `goal` to grade the answer's relevance and provide feedback, and a simple `backstory` as an expert.
4.  **Task Definition:**
    - **`interview_prep_task`:** Assigned to the `interviewer_agent`. Its description includes the job details. Crucially, it has `human_input=True`, which pauses the Crew's execution after the agent formulates the question, prompting the _human user_ to provide their answer in the terminal/notebook interface. The `expected_output` guides the agent to ask only one relevant, non-repeated question.
    - **`coaching_task`:** Assigned to the `coach_agent`. It takes the output of `interview_prep_task` (which includes the question and the user's answer) as `context`. Its `expected_output` is structured feedback with bullet points.
5.  **Crew Execution:**
    - A `Crew` is defined containing both agents and tasks.
    - The `process` is set to `Process.sequential`, ensuring the Interviewer asks first, then the Coach provides feedback.
    - `memory=True` is enabled for the Crew.
    - A `StringKnowledgeSource` is created from a list (`interview_questions`) meant to store asked questions. *Note: In the current notebook structure, this list is updated *after* the `crew.kickoff()` completes one cycle, storing the question asked in that cycle. This helps avoid immediate repetition if the code were run iteratively.*
    - `crew.kickoff()` starts the process.
6.  **Interaction & Output:**
    - The Interviewer agent generates a question and prints it.
    - The execution pauses, waiting for the user to type their answer and press Enter (due to `human_input=True`).
    - Once the user answers, the Coach agent receives the question and answer context.
    - The Coach agent generates feedback based on the user's answer and prints it in Markdown format.
    - The asked question is appended to the `interview_questions` list for the basic memory mechanism.

<img src="./Job Interview Prep Part 1/Job Interview Part 1.jpg" alt="Job Interview Part 1" width="800">

## Technology Stack (Part 1)

- **Python:** Core programming language.
- **CrewAI:** Framework for orchestrating role-playing, autonomous AI agents.
- **LangChain (langchain-openai):** Used for interacting with the OpenAI API and managing LLMs.
- **OpenAI API:** Provides the underlying Large Language Model (GPT-4o in this case).
- **python-dotenv:** For managing environment variables (API keys).
- **Jupyter Notebook:** For interactive development and execution.

## Setup (Part 1)

(See [Common Setup Notes](#common-setup-notes) above for general setup).

1.  **Clone the Repository:**
    ```bash
    git clone <repository-url>
    cd <repository-directory>
    ```
2.  **Create a Virtual Environment (Recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```
3.  **Install Dependencies:**
    (It's recommended to create a `requirements.txt` file)
    ```bash
    pip install crewai langchain-openai python-dotenv jupyter
    # Or if requirements.txt exists:
    # pip install -r requirements.txt
    ```
    _Key libraries include:_ `crewai`, `langchain-openai`, `python-dotenv`.
4.  **Set Up Environment Variables:**
    - Create a file named `.env` in the project's root directory.
    - Add your OpenAI API key to the `.env` file:
      ```
      OPENAI_API_KEY="your_openai_api_key_here"
      ```

## Usage (Part 1)

1.  **Start Jupyter Notebook:**
    ```bash
    jupyter notebook
    ```
2.  **Open the Notebook:** Navigate to and open `CrewAI_Job_Interview_Prep_Part_1.ipynb`.
3.  **Run Cells Sequentially:** Execute the notebook cells one by one.
4.  **Provide Input:** When prompted by the `input()` function in the early cells, enter the requested details (Interviewer Name, Company, Position, Description). You can use the data from ` Data Analytics Working Student.docx` as a reference.
5.  **Interact with the Interviewer:** When the cell executing `crew.kickoff()` runs, the Interviewer agent will print a question. The notebook execution will pause, displaying a prompt like `[crew] Human input needed:` . Type your answer directly into the input field in the notebook/terminal and press Enter.
6.  **Observe Feedback:** After you submit your answer, the Coach agent will process it and print the structured feedback in Markdown format.
7.  **Memory Update:** The final step in the relevant cell appends the asked question to the `interview_questions` list and updates the `StringKnowledgeSource`. If you were to modify the code to run the `crew.kickoff()` in a loop, this mechanism would provide the context of previously asked questions to the Interviewer agent.

## Input Data (`Data Analytics Working Student.docx`) (Part 1)

The `Data Analytics Working Student.docx` file contains example data that can be used when the notebook prompts for input. Its purpose is to provide a quick way to populate the required fields for the simulation.

**Format:** The file contains lines corresponding to the inputs requested by the script:

```

Enter the name of the interviewer: Matthias Glöel
Enter the name of the company: Beat81
Enter the job position: Data Analytics Working Student
Enter the job description: About the Role As a Working Student... [rest of description]

```

_Note: The notebook code currently uses `input()` prompts, it does not automatically read from this file. You need to copy/paste or type the information when prompted._

## Example Interaction Flow (Part 1)

1.  Run the notebook.
2.  Enter interviewer, company, job position, and description when prompted.
3.  The Interviewer Agent ("Matthias Glöel") asks a question like:
    `Could you share an example of a project or experience where you used data analytics to solve a problem or provide insights, and describe the tools and methods you used?`
4.  The notebook prompts for human input. You type your answer:
    `In my university project, I analyzed student enrollment trends using Python (Pandas) for data cleaning and Matplotlib for visualization to identify bottlenecks in course registration.`
5.  You press Enter.
6.  The Coach Agent provides feedback similar to:
    ```markdown
    - **What was good:**
      - Mentioned specific tools (Python, Pandas, Matplotlib).
      - Clearly stated the problem (enrollment trends, bottlenecks).
    - **What was bad:**
      - Could have quantified the impact or insights gained more specifically.
    - **How to take it to the next level:**
      - Detail the specific bottlenecks identified.
      - Mention the outcome – did your analysis lead to any changes or recommendations?
      - Briefly describe the data source and size if relevant.
    ```

---

# Part 2: CrewAI Job Interview Preparation Assistant

## Overview (Part 2)

This project utilizes the CrewAI framework to create an automated job interview preparation assistant. It helps users get ready for specific job interviews by:

1.  **Researching:** Gathering detailed information about the target company and the interviewer (if known).
2.  **Generating Questions:** Creating a list of tailored interview questions based on the job description, company information, and interviewer profile, covering both cultural and positional fit.
3.  **Practicing:** Simulating an interview experience where the user answers generated questions and receives AI-driven feedback on their responses.

The goal is to streamline the interview preparation process, providing relevant background information and a platform for practice and improvement.

## Features (Part 2)

- **Automated Research:** Leverages web scraping and search tools (Serper) to find recent news, developments, and professional background information on the company and interviewer.
- **Tailored Question Generation:** Uses an LLM (GPT-4o) to create relevant interview questions based on the specific job role, description, and researched context.
- **Information Export:** Saves the generated company report, interviewer report, and interview questions into separate `.txt` files for easy review.
- **Interactive Interview Practice:** Engages the user in a mock interview, asking generated questions one by one.
- **Constructive Feedback:** Provides structured feedback (positives and negatives) on the user's answers, facilitated by an AI coach agent.
- **Iterative Improvement:** Allows for potential iterative feedback cycles within the practice session (though the example shows one round).

## Workflow (Part 2)

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

_(A visual representation of this workflow is provided)_

<img src="./Job Interview Prep Part 2/Interview prep part 2.jpg" alt="Job Interview Part 2" width="800">

## How to Use (Part 2)

**Prerequisites:**

- Python 3.9+
- OpenAI API Key
- Serper API Key (for the `SerperDevTool`)

**Setup:**

(See [Common Setup Notes](#common-setup-notes) above for general setup).

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

## Core Components (CrewAI) (Part 2)

- **Research Agent:** Responsible for conducting web searches and scraping websites to gather information about the company and interviewer. Uses `SerperDevTool` and `ScrapeWebsiteTool`.
- **Coach Agent:** Acts as an interview coach. Responsible for generating interview questions based on research and providing structured feedback on user answers during the practice session. Also serves as the manager agent in the practice crew.
- **Interviewer Agent:** Simulates the interviewer during the practice session, posing questions to the user. Its persona can be influenced by the researched interviewer's details.

## Dependencies (Part 2)

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

---

# Part 3: CrewAI YouTube Partnership Scoring

## Overview/Description (Part 3)

This project implements an automated system using CrewAI to evaluate and score potential YouTube channel partnerships for the **Zero to Mastery (ZTM)** brand. Sponsored by Andrei from ZTM, the goal is to streamline the assessment process, ensuring potential partners align with ZTM's core values: _Quality education, community-driven growth, career-focused learning, and up-to-date tech stacks_.

The system researches channels focusing on programming, software engineering, or tech career advice, analyzes their content and sentiment, and provides a score to aid in selecting the best partners quickly and consistently.

## Features (Part 3)

- **Automated Research:** Researches YouTube channel focus, content type, approximate subscriber count, and engagement levels.
- **Audience Alignment Check:** Assesses if the channel's audience aligns with ZTM's target demographic (motivated learners in tech).
- **Video Analysis:** Identifies and summarizes topics of popular videos on the channel.
- **External Sentiment Analysis:** Uses Google Search (specifically targeting Reddit) to gauge public sentiment and feedback about the channel.
- **Partnership Scoring:** Generates a numerical score (0-10) based on the research findings and ZTM's brand values.
- **Justification:** Provides a concise text justification explaining the rationale behind the assigned score.

## Technology Stack (Part 3)

- **Language:** Python 3.11
- **AI Framework:** CrewAI
- **LLM Orchestration:** LangChain (implicitly used by CrewAI)
- **LLM:** OpenAI GPT-4o (via `langchain-openai`)
- **Search/Scraping Tools (CrewAI Tools):**
  - `SerperDevTool` (Google Search API)
  - `ScrapeWebsiteTool`
  - `YoutubeChannelSearchTool`
  - `YoutubeVideoSearchTool`
- **Data Validation:** Pydantic
- **Environment Variables:** python-dotenv
- **Notebook:** Jupyter Notebook / IPython

## How it Works (Part 3)

This project leverages the CrewAI framework, utilizing AI agents to perform specific tasks sequentially:

### Agents (Part 3)

1.  **Influencer Researcher (`research_agent`):** Responsible for gathering information about the target YouTube channel, its content, audience, and external sentiment using various web search and scraping tools.
2.  **Partnership Scoring Agent (`scoring_agent`):** Responsible for evaluating the research findings (provided as context) against ZTM's brand values and generating a final partnership score and justification.

### Tasks (Part 3)

The crew executes the following tasks in order (`Process.sequential`):

1.  **Channel Research:** Gathers basic channel info, focus, and audience details.
2.  **Video Research:** Identifies popular videos and their topics.
3.  **Sentiment Analysis:** Searches external sources (like Reddit) for reviews and opinions.
4.  **Scoring:** Synthesizes all gathered information to produce the final score and justification using a Pydantic model for structured output.

---

## Project Analysis (Part 3)

This section provides a detailed analysis based on the project briefing and the implementation in the Jupyter Notebook (`Capstone Project - Starter Code.ipynb`).

### Project Purpose (Part 3)

- The project aims to create an automated system using CrewAI to evaluate and score potential YouTube channel partnerships for the "Zero to Mastery" (ZTM) brand.
- It solves the problem of manually researching and assessing numerous channels, intending to speed up the partnership assessment process and ensure selected partners align with ZTM's core values (quality education, community, career focus, up-to-date tech) and target audience (motivated learners in programming, software engineering, tech careers).

### Workflow Implementation (Part 3)

- **Setup:** Loads environment variables (API keys) using `dotenv` and imports necessary libraries (CrewAI, LangChain, OpenAI, CrewAI Tools, Pydantic). Initializes the `ChatOpenAI` LLM (`gpt-4o`).
- **Data Input:** Defines Python dictionaries `brand_info` (ZTM's identity, values, ideal partner) and `youtube_lead` (target channel name and URL).
- **Agent Definition:** Defines `research_agent` (researches channel suitability using tools) and `scoring_agent` (scores partnership based on research context).
- **Task Definition:** Defines sequential tasks: `channel_research_task`, `videos_research_task`, `sentiment_task`, and `scoring_task`. The scoring task uses the output of the previous three as context and structures its output using a Pydantic `Score` model.
- **Crew Configuration:** Assembles a `Crew` with the agents and tasks, set to run sequentially (`Process.sequential`).
- **Execution:** `crew.kickoff()` initiates the process.
- **Output:** The final `result` object contains task outputs. The notebook extracts and displays the raw output from each task using Markdown. The `scoring_task` output provides the structured score and justification.

<img src="./CAPTONE PROJECT_ Partnerships/crewai-partnership-evaluation-flowchart.jpg" alt="CREWAI Partnership Evaluation Flowchart" width="800">

### Technology Stack (Detailed) (Part 3)

- **Core Framework:** CrewAI
- **LLM Orchestration:** LangChain (used implicitly by CrewAI)
- **LLM Provider:** OpenAI (via `langchain-openai`)
- **LLM Model:** GPT-4o
- **AI Agents/Tasks:** CrewAI (`Agent`, `Task`, `Crew`, `Process`)
- **Tools:** CrewAI Tools:
  - `SerperDevTool` (for Google Search)
  - `ScrapeWebsiteTool` (for reading website content)
  - `YoutubeChannelSearchTool` (for searching channel content)
  - `YoutubeVideoSearchTool` (for searching specific video content)
- **Data Validation/Structuring:** Pydantic (`BaseModel`)
- **Environment Management:** python-dotenv
- **Notebook Environment:** Jupyter Notebook / IPython (`display`, `Markdown`)
- **Language:** Python

### Data Inputs (Part 3)

- `brand_info`: A dictionary containing ZTM's `name`, `description`, `brand_values`, and `ideal_partner` profile.
- `youtube_lead`: A dictionary containing the `name` and `channel_url` of the YouTube channel being evaluated.

### AI Agents (Detailed) (Part 3)

- **`research_agent`**:
  - `role`: "Influencer researcher"
  - `goal`: Research the specified YouTube channel (`Theo - t3․gg`) to assess suitability for Zero to Mastery.
  - `backstory`: Expert in influencer partnerships aligned with ZTM's values (Quality education, community-driven growth, career-focused learning, up-to-date tech stacks).
  - `tools`: `SerperDevTool`, `ScrapeWebsiteTool`, `YoutubeChannelSearchTool`, `YoutubeVideoSearchTool`.
- **`scoring_agent`**:
  - `role`: "Partnership Scoring Agent"
  - `goal`: Score the partnership between ZTM and the YouTube channel, assessing based on ZTM's brand values.
  - `backstory`: Expert in Influencer Marketing working for ZTM.
  - `tools`: None explicitly assigned (relies on context from previous tasks).

### Tasks (Detailed) (Part 3)

- **`channel_research_task`**:
  - `description`: Research channel focus, content type, subscriber count, engagement, and audience alignment with ZTM.
  - `expected_output`: "A research report on the channel's key attributes and audience"
  - `agent`: `research_agent`
- **`videos_research_task`**:
  - `description`: Research most popular videos, summarize topics, analyze audiences.
  - `expected_output`: "A research report on the most popular videos and their topics"
  - `agent`: `research_agent`
- **`sentiment_task`**:
  - `description`: Conduct sentiment analysis via Google Search (mentioning Reddit) for feedback, reviews, discussions.
  - `expected_output`: "A sentiment analysis report on the channel's"
  - `agent`: `research_agent`
- **`scoring_task`**:
  - `description`: Score the partnership between ZTM and the channel.
  - `expected_output`: "A score between 1 and 10 and justification for the scoring. An explanation of the score indicating what fits the brand and what does not"
  - `agent`: `scoring_agent`
  - `context`: Depends on the completion and output of `channel_research_task`, `videos_research_task`, `sentiment_task`.
  - `output_pydantic`: Uses the `Score` BaseModel (`score: int`, `justification: str`) to structure the output.

### Crew Configuration (Part 3)

- The `Crew` is configured with a list containing both agents (`research_agent`, `scoring_agent`) and a list containing all four tasks in the desired order.
- It uses `Process.sequential`, ensuring tasks run one after another, with the output of earlier tasks available as context for later ones (specifically for the `scoring_task`).

### Output Details (Part 3)

- The final output of the crew execution is captured in the `result` object.
- The notebook specifically accesses `result.tasks_output` to get the raw string outputs from each of the four tasks.
- The most critical output is from the `scoring_task`, which adheres to the `Score` Pydantic model, providing a JSON-like string containing an integer `score` (0-10) and a string `justification`.
- These individual outputs are displayed clearly at the end using Markdown formatting.

### Alignment with Briefing (Part 3)

- **Requirement 1 (Research channel focus, engagement, audience):** Met by `channel_research_task`.
- **Requirement 2 (Examine videos):** Met by `videos_research_task`.
- **Requirement 3 (Check external sentiment):** Met by `sentiment_task`.
- **Requirement 4 (Score with justification):** Met by `scoring_task` using the `Score` Pydantic model.
- **Implementation Guideline (Key findings, score 0-10, report):** Met. Task outputs are findings, scoring task provides score/justification, collectively forming the report elements.
- **Brand Objectives:** Integrated into agent goals/backstories and task context.
- **Goal (Speed up, Ensure match):** Directly addressed by the automated workflow and structured analysis.

### Potential Gaps/Improvements (Part 3)

- **Depth of Video Analysis:** The briefing asks for examining specific video details (views, comments, sentiment). The notebook currently focuses more on identifying popular video _topics_ via search rather than deep-diving into individual video content/comments sentiment using tools like `YoutubeVideoSearchTool`.
- **Report Synthesis:** The notebook outputs raw results from each task. A potential improvement could be adding a final task/agent to synthesize these into a single, unified report document as requested in the briefing ("report detailing the recommendation").
- **Score Range Consistency:** Minor difference between briefing (0-10) and task description (1-10), though the implementation correctly allows for 0-10.

---

## Setup and Installation (Part 3)

(See [Common Setup Notes](#common-setup-notes) above for general setup).

1.  **Clone the repository:**
    ```bash
    git clone <repository-url>
    cd <repository-directory>
    ```
2.  **Create and activate a virtual environment (recommended):**
    ```bash
    python -m venv venv
    # On Windows
    venv\Scripts\activate
    # On macOS/Linux
    source venv/bin/activate
    ```
3.  **Install dependencies:**
    ```bash
    pip install -r requirements.txt
    # Or manually: pip install crewai crewai-tools langchain-openai pydantic python-dotenv jupyter
    ```
    _(Note: You'll need to create a `requirements.txt` file based on the notebook imports, e.g., `pip freeze > requirements.txt` after installing manually)_
4.  **Configure Environment Variables:**
    - Create a `.env` file in the project root directory.
    - Add your API keys to this file. You can use `.env.example` as a template:
      **.env.example:**
    ```env
    OPENAI_API_KEY="your_openai_api_key_here"
    SERPER_API_KEY="your_serper_api_key_here"
    # Add any other necessary keys (e.g., specific YouTube API keys if Youtube tools require them)
    OPENAI_MODEL_NAME="gpt-4o" # Optional: specify model if not hardcoded
    ```
    - **Important:** Ensure the `.env` file is listed in your `.gitignore` file to avoid committing secrets.

## Usage (Part 3)

1.  **Ensure your `.env` file is populated** with the necessary API keys (OpenAI and Serper).
2.  **Open the Jupyter Notebook:**
    ```bash
    jupyter notebook "Capstone Project - Starter Code.ipynb"
    ```
3.  **Modify Inputs (Optional):** Update the `brand_info` and `youtube_lead` dictionaries in the notebook cells if you want to evaluate a different brand or channel.
4.  **Run the cells:** Execute the notebook cells sequentially from top to bottom.
5.  **View Results:** The outputs from each research task (Channel Research, Videos Research, Sentiment Analysis) and the final Scoring output will be displayed in the final cells of the notebook.

## Output Example (Part 3)

The final output from the scoring task will be a JSON-like structure adhering to the `Score` model:

```json
{
  "score": 8,
  "justification": "The partnership between Zero to Mastery and Theo - t3․gg scores an 8 out of 10. Theo's YouTube channel aligns well with Zero to Mastery's core values... His focus on full stack TypeScript and modern web development practices resonates with Zero to Mastery's audience... The channel's significant subscriber count and high engagement levels indicate a strong potential... However, some criticisms exist regarding Theo's communication style and perceived influence by sponsorships... Despite these concerns, the partnership remains highly beneficial..."
}
```

---

# Part 4: CrewAI PDF Invoice Processing Automation

Automated workflow using CrewAI to extract, structure, and analyze data from PDF invoices.

## Overview (Part 4)

This project demonstrates how to automate the processing of PDF invoices using the CrewAI framework. It leverages AI agents to perform tasks such as searching for specific invoices within a directory, extracting key information, structuring the data into a CSV format, and answering questions based on the content of the invoices.

The primary goal is to reduce the manual effort involved in handling large volumes of invoices, extracting relevant data points, and performing basic analysis or querying on that data.

## Features (Part 4)

- **Targeted PDF Retrieval:** Locates specific PDF invoice files within a designated folder based on user-defined criteria (e.g., issuer name).
- **Automated Data Extraction:** Extracts relevant text and data fields from the identified PDF invoices.
- **Data Structuring:** Formats the extracted unstructured data into a structured CSV format with predefined columns.
- **Query Answering:** Analyzes the extracted data to answer specific questions posed by the user (e.g., finding the total amount or personnel involved in the latest invoice).
- **Customizable Workflow:** Allows easy modification of input parameters (folder, search criteria, output columns, questions) to adapt to different needs.

## Workflow (Part 4)

The automation process follows a sequential workflow executed by a CrewAI crew:

1.  **Input:** The process starts with user-defined inputs specifying the target folder, invoice requirements, desired output columns, and a specific question.
2.  **Retrieve & Extract (Assistant Agent):** The first agent searches the specified folder (`invoices/`) using `DirectoryReadTool` to find files matching the requirements. It then uses `PDFSearchTool` to extract relevant text content from the identified PDF invoices (e.g., `Braun_2022-10.pdf`, `simonis_2025-03.pdf`).
3.  **Structure (Organizer Agent):** The second agent takes the raw extracted text, processes it, and structures the relevant information into a CSV format according to the specified columns. It ensures correct formatting for dates and numeric values.
4.  **Analyze (Analyst Agent):** The third agent analyzes the structured data (or the raw extracted data from the relevant invoice) to answer the specific question provided in the initial inputs.
5.  **Output:** The workflow generates several outputs: the raw extracted text, the structured data as a CSV string, the final answer to the user's question, and saves the structured data to `result.csv`.

The flowchart below visually represents this automation process:

<img src="./Business Automation-PDF/Business Automation-PDF.png" alt="Business Automation-PDF Flowchart" width="1000">

## Components (Part 4)

### Jupyter Notebook (`Business Automation - PDFs.ipynb`)

This notebook serves as the main script for the project. It contains the Python code to:

- Load necessary libraries and environment variables.
- Define the input parameters for the workflow.
- Instantiate the CrewAI agents (`assistant_agent`, `organizer_agent`, `analyst_agent`) with their specific roles, goals, backstories, and tools.
- Define the CrewAI tasks (`assistant_task`, `organizer_task`, `analyst_task`) assigned to each agent.
- Assemble and configure the CrewAI crew.
- Kick off the crew execution process.
- Display and save the results.

### CrewAI Agents (Part 4)

Three distinct agents collaborate to achieve the overall goal:

1.  **Invoice Retrieval Assistant (`assistant_agent`)**
    - **Role:** Invoice Retrieval Assistant
    - **Goal:** Search files within the specified folder (`inputs['folder']`), locate documents matching the criteria (`inputs['requirements']`), and extract relevant data.
    - **Backstory:** An efficient assistant specializing in organizing and retrieving data from documents.
    - **Tools:**
      - `DirectoryReadTool`: To list files in the target directory.
      - `PDFSearchTool`: To search and extract content from PDF files based on a query.
2.  **Data Organization Assistant (`organizer_agent`)**
    - **Role:** Data Organization Assistant
    - **Goal:** Process provided data and format it strictly as a CSV file with specified columns (`inputs['columns']`), ensuring correct date and numeric formatting.
    - **Backstory:** An efficient assistant specializing in structuring and organizing data into CSV format.
    - **Tools:** None (relies on LLM capabilities for formatting).
3.  **Data Analyst Assistant (`analyst_agent`)**
    - **Role:** Data Analyst Assistant
    - **Goal:** Analyze provided data to accurately answer a specific question (`inputs['question']`) based strictly on facts and data.
    - **Backstory:** A detail-oriented data analyst skilled at extracting insights and answering questions based on data.
    - **Tools:** None (relies on LLM capabilities for analysis).

### CrewAI Tasks (Part 4)

Each agent is assigned a specific task:

1.  **Assistant Task (`assistant_task`)**
    - **Description:** Search the `inputs['folder']` for files matching `inputs['requirements']` and extract all data from the relevant files.
    - **Expected Output:** The extracted data from the relevant files, formatted for clarity.
    - **Agent:** `assistant_agent`
2.  **Organizer Task (`organizer_task`)**
    - **Description:** Format the data provided by the previous task into a CSV file with columns specified in `inputs['columns']`. Ensure correct date (DD-MM-YYYY) and numeric (decimal point) formatting. Save strictly as CSV without extra text.
    - **Expected Output:** A properly formatted CSV string containing the correct values in the specified columns.
    - **Agent:** `organizer_agent`
3.  **Analyst Task (`analyst_task`)**
    - **Description:** Analyze the provided data (from previous tasks) to answer the question defined in `inputs['question']`. Base the response strictly on data.
    - **Expected Output:** A correct, data-backed answer along with identification of the source file if relevant.
    - **Agent:** `analyst_agent`

### Input Parameters (Part 4)

The workflow is customized using the `inputs` dictionary defined in the notebook:

```python
inputs = {
    'folder': 'invoices',
    'requirements': 'all invoices from Simonis and Braun',
    'columns': ['Date', "Description", 'Total Amount', 'Issuer', "Receiver"],
    'question' : 'what is the total amount invoiced and all the names of personnel involved in the latest invoice file'
}
```

- `folder`: The directory containing the PDF invoices to process.
- `requirements`: Criteria used by the `assistant_agent` to identify relevant invoices.
- `columns`: The desired column headers for the output CSV file.
- `question`: The specific question the `analyst_agent` needs to answer based on the invoice data.

### Input Files (`invoices/`) (Part 4)

The `invoices/` directory should contain the PDF invoice files that the agents will process. The example run in the notebook specifically processes:

- `invoices/Braun_2022-10.pdf`
- `invoices/simonis_2025-03.pdf`

### Output File (`result.csv`) (Part 4)

This file is generated by the `organizer_agent` (and saved via pandas in the notebook). It contains the structured data extracted from the processed invoices. The columns are determined by `inputs['columns']`.

Example `result.csv` structure (based on the notebook run):

```csv
,Date,Description,Total Amount,Issuer,Receiver
0,04-10-2022,Quis ut excepturi quia odit.,34669.16,Braun,Mr. Cormier
1,04-10-2022,Et qui voluptatem libero.,43820.51,Braun,Mr. Cormier
2,04-10-2022,Accusamus ea autem.,76408.76,Braun,Mr. Cormier
3,04-10-2022,Consectetur ratione explicabo.,13194.31,Braun,Mr. Cormier
4,05-02-2025,Deleniti consequatur hic omnis.,54209.20,Simonis LLC,Norwood Spinka
5,05-02-2025,Quis qui cum eligendi.,13338.94,Simonis LLC,Norwood Spinka
6,05-02-2025,Aut error voluptatem voluptatem.,80042.66,Simonis LLC,Norwood Spinka
7,05-02-2025,Assumenda nemo.,4959.00,Simonis LLC,Norwood Spinka
8,05-02-2025,Qui ab.,23527.32,Simonis LLC,Norwood Spinka
```

_(Note: The first unnamed column is the pandas DataFrame index)_

## Setup and Installation (Part 4)

(See [Common Setup Notes](#common-setup-notes) above for general setup).

1.  **Prerequisites:**
    - Python 3.x installed.
    - Git installed.
2.  **Clone Repository:**
    ```bash
    git clone <repository-url>
    cd <repository-directory>
    ```
3.  **Install Dependencies:**
    It's recommended to use a virtual environment:
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```
    Install the required libraries:
    ```bash
    pip install crewai crewai-tools openai python-dotenv pandas ipython langchain-openai
    # Or if a requirements.txt file is provided:
    # pip install -r requirements.txt
    ```
4.  **Environment Variables:**
    This project requires an OpenAI API key to function.
    - Create a file named `.env` in the root directory of the project.
    - Add your OpenAI API key to the `.env` file like this:
      ```
      OPENAI_API_KEY='your_api_key_here'
      ```
    - The notebook uses `python-dotenv` (`load_dotenv(find_dotenv(), override=True)`) to load this key automatically.

## Usage (Part 4)

1.  Ensure all setup steps (cloning, installation, `.env` file) are completed.
2.  Place the PDF invoice files you want to process into the `invoices/` directory (or update the `inputs['folder']` variable in the notebook).
3.  Open the `Business Automation - PDFs.ipynb` notebook using Jupyter Lab or Jupyter Notebook.
4.  **Optional:** Modify the `inputs` dictionary within the notebook to change the target folder, invoice requirements, desired output columns, or the question to be answered.
5.  Execute the notebook cells sequentially from top to bottom.
6.  The results, including the extracted data, the formatted CSV string, the answer to the question, and the saved `result.csv` file, will be generated upon successful execution.

## Output Examples (Part 4)

The notebook execution produces several key outputs:

1.  **Raw Extracted Data (Assistant Agent Output):** Text content extracted from the relevant PDFs.

    ```
    Braun Invoice:
    ```

    Date: 2022-10-04
    Invoice No.: 31500
    Customer: Mr. Cormier
    Address: Stehr-Heaney, Donato Cormier, Leopold Forks, 711 24085 McCluremouth, Saint Pierre and Miquelon
    Items:

    1.  Amount: 73 Pcs, Description: Quis ut excepturi quia odit., Price: 474,92 €, Total: 34.669,16 €
        ...
        Invoice total: 184.902,01 €
        ...

    ```

    Simonis Invoice:
    ```

    Date: 05.02.2025
    Invoice No.: 84951
    Customer: Norwood Spinka
    Address: Aurelia Wall 784, 42644-0051 South Maudieland
    Company: Simonis LLC, Barney Village 891, 24163-6226 Kaseytown
    Items:

    1.  Description: Deleniti consequatur hic omnis., Price: 918,80 €, Amount: 59, Total: 54.209,20 €
        ...
        Invoice total: 188.402,52 €
        ...

    ```

    ```

2.  **Structured CSV Data (Organizer Agent Output / `result.csv`):**

    ```csv
    Date,Description,Total Amount,Issuer,Receiver
    04-10-2022,Quis ut excepturi quia odit.,34669.16,Braun,Mr. Cormier
    04-10-2022,Et qui voluptatem libero.,43820.51,Braun,Mr. Cormier
    04-10-2022,Accusamus ea autem.,76408.76,Braun,Mr. Cormier
    04-10-2022,Consectetur ratione explicabo.,13194.31,Braun,Mr. Cormier
    05-02-2025,Deleniti consequatur hic omnis.,54209.20,Simonis LLC,Norwood Spinka
    05-02-2025,Quis qui cum eligendi.,13338.94,Simonis LLC,Norwood Spinka
    05-02-2025,Aut error voluptatem voluptatem.,80042.66,Simonis LLC,Norwood Spinka
    05-02-2025,Assumenda nemo.,4959.00,Simonis LLC,Norwood Spinka
    05-02-2025,Qui ab.,23527.32,Simonis LLC,Norwood Spinka
    ```

3.  **Final Answer (Analyst Agent Output):**
    > The total amount invoiced in the latest invoice file, which is the Simonis Invoice dated 05.02.2025, is 188.402,52 €. The personnel involved are Garry Kilback (CEO of Simonis LLC) and Norwood Spinka (Customer).

## Technologies Used (Part 4)

- **Python:** Core programming language.
- **CrewAI:** Framework for orchestrating autonomous AI agents.
- **LangChain / OpenAI API:** Used via `langchain-openai` for the LLM powering the agents.
- **CrewAI Tools:**
  - `DirectoryReadTool`: For listing directory contents.
  - `PDFSearchTool`: For semantic search within PDF documents.
- **Pandas:** For data manipulation and saving the output CSV.
- **Jupyter Notebook:** For interactive development and execution.
- **python-dotenv:** For managing environment variables (API keys).
