# Document Search Bot

The Document Search Bot is a web application that allows users to perform searches using a search bot implemented with React on the client side and Flask on the server side. The application showcases the integration of a frontend React app with a Python-based Flask backend.

## Table of Contents

- [Description](#description)
- [Installation](#installation)
- [Usage](#usage)
- [Folder Structure](#folder-structure)
- [Testing](#testing)

## Description

The Search Bot App is a web application that allows users to perform searches using a search bot implemented with React on the client side and Flask on the server side. The application is designed to showcase the integration of a frontend React app with a Python-based Flask backend.


## Installation

To run the Search Bot App, follow the steps below:

### Client (React App)

1. Navigate to the `client` folder:

    ```bash
    cd client
    ```

2. Install dependencies:

    ```bash
    npm install
    ```

### Server (Flask App)

1. Navigate to the `server` folder:

    ```bash
    cd server
    ```

2. Create and activate a virtual environment (recommended):

    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows, use `venv\Scripts\activate`
    ```

3. Install dependencies:

    ```bash
    pip install -r requirements.txt
    ```

4. Set up OpenAI API key:

   Obtain an API key from OpenAI and set it in the `run.py` file. Open the `run.py` file and locate the `openai.api_key` variable. Replace `'YOUR_OPENAI_API_KEY'` with your actual OpenAI API key.

## Usage

1. Run the Flask server:

    ```bash
    python run.py
    ```

   This will start the Flask server, and the app will be accessible at `http://localhost:5000`.

2. Run the React app:

    ```bash
    cd client
    npm start
    ```

   The React app will be accessible at `http://localhost:3000`.





