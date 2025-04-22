# CrewAI Job Interview Preparation Simulator

## Overview

This project demonstrates how to use CrewAI, LangChain, and OpenAI's GPT models to simulate a job interview preparation session. It features two AI agents: an Interviewer who asks job-relevant questions and a Coach who provides feedback on the user's answers. The simulation is interactive, allowing the user to practice their responses in real-time.

## Features

- **AI Interviewer Agent:** Simulates a specific interviewer from a target company, asking questions relevant to a defined job position and description.
- **AI Coach Agent:** Evaluates the user's answer and provides structured feedback (Good, Bad, How to Improve).
- **Interactive Q&A:** Utilizes CrewAI's `human_input=True` feature to pause execution and wait for the user's typed answer.
- **Sequential Workflow:** Agents operate in a defined order: Interviewer asks -> User answers -> Coach gives feedback.
- **Contextual Feedback:** The Coach agent uses the context from the Interviewer's task (the question asked and the user's answer) to generate relevant feedback.
- **Basic Memory:** Implements a simple memory mechanism using `StringKnowledgeSource` to store previously asked questions, aiming to prevent the Interviewer from asking the exact same question again in subsequent interactions (within a potential iterative loop, though the notebook runs one cycle).

## How it Works

1.  **Initialization:**

    - Loads necessary environment variables (specifically `OPENAI_API_KEY`).
    - Defines the OpenAI LLM to be used (e.g., `gpt-4o`).

2.  **User Input:**

    - The script prompts the user to enter the Interviewer's name, Company name, Job Position, and Job Description using Python's `input()` function.
    - Alternatively, the `generated_file.txt` provides example data that can be used as input or adapted into the code.

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

## Technology Stack

- **Python:** Core programming language.
- **CrewAI:** Framework for orchestrating role-playing, autonomous AI agents.
- **LangChain (langchain-openai):** Used for interacting with the OpenAI API and managing LLMs.
- **OpenAI API:** Provides the underlying Large Language Model (GPT-4o in this case).
- **python-dotenv:** For managing environment variables (API keys).
- **Jupyter Notebook:** For interactive development and execution.

## Setup

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

## Usage

1.  **Start Jupyter Notebook:**
    ```bash
    jupyter notebook
    ```
2.  **Open the Notebook:** Navigate to and open `CrewAI_Job_Interview_Prep_Part_1.ipynb`.
3.  **Run Cells Sequentially:** Execute the notebook cells one by one.
4.  **Provide Input:** When prompted by the `input()` function in the early cells, enter the requested details (Interviewer Name, Company, Position, Description). You can use the data from `generated_file.txt` as a reference.
5.  **Interact with the Interviewer:** When the cell executing `crew.kickoff()` runs, the Interviewer agent will print a question. The notebook execution will pause, displaying a prompt like `[crew] Human input needed:` . Type your answer directly into the input field in the notebook/terminal and press Enter.
6.  **Observe Feedback:** After you submit your answer, the Coach agent will process it and print the structured feedback in Markdown format.
7.  **Memory Update:** The final step in the relevant cell appends the asked question to the `interview_questions` list and updates the `StringKnowledgeSource`. If you were to modify the code to run the `crew.kickoff()` in a loop, this mechanism would provide the context of previously asked questions to the Interviewer agent.

## Input Data (`generated_file.txt`)

The `generated_file.txt` file contains example data that can be used when the notebook prompts for input. Its purpose is to provide a quick way to populate the required fields for the simulation.

**Format:** The file contains lines corresponding to the inputs requested by the script:

```

Enter the name of the interviewer: Matthias Glöel
Enter the name of the company: Beat81
Enter the job position: Data Analytics Working Student
Enter the job description: About the Role As a Working Student... [rest of description]

```

_Note: The notebook code currently uses `input()` prompts, it does not automatically read from this file. You need to copy/paste or type the information when prompted._

## Example Interaction Flow

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
