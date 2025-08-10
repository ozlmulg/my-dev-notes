# My Dev Notes

This repository is my personal digital notebook containing development notes, cheatsheets, newly learned topics, and
professional books to read.

## Contents

- Cheatsheets related to Docker, Kubernetes, and other technologies
- Development notes and new knowledge I’ve acquired
- Lists of books to read
- Personal projects and ideas

## Installation and Usage

1. Clone the repository:
   ```bash
   git clone https://github.com/kullaniciAdi/my-dev-notes.git
   cd my-dev-notes
   ```

2. Create and activate a virtual environment:
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
    ```

3. Install the required packages:
   ```bash
   pip install mkdocs-material
   ```

4. Start the site locally (Open your browser and go to http://127.0.0.1:8000):
   ```bash
   mkdocs serve
   ```

5. To deploy the site to GitHub Pages:
   ```bash
   mkdocs gh-deploy
   ```

6. To deactivate virtual environment:
   ```bash
   deactivate
   ```

7. For "Address already in use" error:
   ```bash
   lsof -i :8000
   kill -9 PID
   mkdocs serve -a 127.0.0.1:8001 # run on a different port
   ```