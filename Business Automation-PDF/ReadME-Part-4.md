# CrewAI PDF Invoice Processing Automation

Automated workflow using CrewAI to extract, structure, and analyze data from PDF invoices.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Workflow](#workflow)
- [Components](#components)
  - [Jupyter Notebook (`Business Automation - PDFs.ipynb`)](#jupyter-notebook-business-automation---pdfsipynb)
  - [CrewAI Agents](#crewai-agents)
  - [CrewAI Tasks](#crewai-tasks)
  - [Input Parameters](#input-parameters)
  - [Input Files (`invoices/`)](#input-files-invoices)
  - [Output File (`result.csv`)](#output-file-resultcsv)
- [Setup and Installation](#setup-and-installation)
- [Usage](#usage)
- [Output Examples](#output-examples)
- [Technologies Used](#technologies-used)

## Overview

This project demonstrates how to automate the processing of PDF invoices using the CrewAI framework. It leverages AI agents to perform tasks such as searching for specific invoices within a directory, extracting key information, structuring the data into a CSV format, and answering questions based on the content of the invoices.

The primary goal is to reduce the manual effort involved in handling large volumes of invoices, extracting relevant data points, and performing basic analysis or querying on that data.

## Features

- **Targeted PDF Retrieval:** Locates specific PDF invoice files within a designated folder based on user-defined criteria (e.g., issuer name).
- **Automated Data Extraction:** Extracts relevant text and data fields from the identified PDF invoices.
- **Data Structuring:** Formats the extracted unstructured data into a structured CSV format with predefined columns.
- **Query Answering:** Analyzes the extracted data to answer specific questions posed by the user (e.g., finding the total amount or personnel involved in the latest invoice).
- **Customizable Workflow:** Allows easy modification of input parameters (folder, search criteria, output columns, questions) to adapt to different needs.

## Workflow

The automation process follows a sequential workflow executed by a CrewAI crew:

1.  **Input:** The process starts with user-defined inputs specifying the target folder, invoice requirements, desired output columns, and a specific question.
2.  **Retrieve & Extract (Assistant Agent):** The first agent searches the specified folder (`invoices/`) using `DirectoryReadTool` to find files matching the requirements. It then uses `PDFSearchTool` to extract relevant text content from the identified PDF invoices (e.g., `Braun_2022-10.pdf`, `simonis_2025-03.pdf`).
3.  **Structure (Organizer Agent):** The second agent takes the raw extracted text, processes it, and structures the relevant information into a CSV format according to the specified columns. It ensures correct formatting for dates and numeric values.
4.  **Analyze (Analyst Agent):** The third agent analyzes the structured data (or the raw extracted data from the relevant invoice) to answer the specific question provided in the initial inputs.
5.  **Output:** The workflow generates several outputs: the raw extracted text, the structured data as a CSV string, the final answer to the user's question, and saves the structured data to `result.csv`.

The flowchart below visually represents this automation process:

<img src="./Business Automation-PDF.png"/>

## Components

### Jupyter Notebook (`Business Automation - PDFs.ipynb`)

This notebook serves as the main script for the project. It contains the Python code to:

- Load necessary libraries and environment variables.
- Define the input parameters for the workflow.
- Instantiate the CrewAI agents (`assistant_agent`, `organizer_agent`, `analyst_agent`) with their specific roles, goals, backstories, and tools.
- Define the CrewAI tasks (`assistant_task`, `organizer_task`, `analyst_task`) assigned to each agent.
- Assemble and configure the CrewAI crew.
- Kick off the crew execution process.
- Display and save the results.

### CrewAI Agents

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

### CrewAI Tasks

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

### Input Parameters

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

### Input Files (`invoices/`)

The `invoices/` directory should contain the PDF invoice files that the agents will process. The example run in the notebook specifically processes:

- `invoices/Braun_2022-10.pdf`
- `invoices/simonis_2025-03.pdf`

### Output File (`result.csv`)

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

## Setup and Installation

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

## Usage

1.  Ensure all setup steps (cloning, installation, `.env` file) are completed.
2.  Place the PDF invoice files you want to process into the `invoices/` directory (or update the `inputs['folder']` variable in the notebook).
3.  Open the `Business Automation - PDFs.ipynb` notebook using Jupyter Lab or Jupyter Notebook.
4.  **Optional:** Modify the `inputs` dictionary within the notebook to change the target folder, invoice requirements, desired output columns, or the question to be answered.
5.  Execute the notebook cells sequentially from top to bottom.
6.  The results, including the extracted data, the formatted CSV string, the answer to the question, and the saved `result.csv` file, will be generated upon successful execution.

## Output Examples

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

    1. Amount: 73 Pcs, Description: Quis ut excepturi quia odit., Price: 474,92 €, Total: 34.669,16 €
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

    1. Description: Deleniti consequatur hic omnis., Price: 918,80 €, Amount: 59, Total: 54.209,20 €
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

## Technologies Used

- **Python:** Core programming language.
- **CrewAI:** Framework for orchestrating autonomous AI agents.
- **LangChain / OpenAI API:** Used via `langchain-openai` for the LLM powering the agents.
- **CrewAI Tools:**
  - `DirectoryReadTool`: For listing directory contents.
  - `PDFSearchTool`: For semantic search within PDF documents.
- **Pandas:** For data manipulation and saving the output CSV.
- **Jupyter Notebook:** For interactive development and execution.
- **python-dotenv:** For managing environment variables (API keys).
