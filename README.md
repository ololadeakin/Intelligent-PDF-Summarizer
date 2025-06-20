# 📄 Intelligent PDF Summarizer – Azure Durable Functions

This project is an intelligent document summarization pipeline built with **Azure Durable Functions**, **Blob Storage triggers**, **Azure OpenAI**, and **Form Recognizer**. It automatically:

1. Listens for new PDF uploads in Azure Blob Storage (`input/` container),
2. Extracts structured content using Document Intelligence (Form Recognizer),
3. Summarizes the extracted content via Azure OpenAI (Chat Completions),
4. Uploads the summarized text to the `output/` container.

---
## 📍 Demo Video

🎮 Watch the full walkthrough on YouTube:

👉 [![Watch on YouTube](https://img.shields.io/badge/Watch%20Demo-YouTube-red?style=for-the-badge\&logo=youtube)](https://youtu.be/FPLK_Bhr2-0)

---
## 🚀 How It Works

1. You upload a `.pdf` file to the **`input`** container in Azure Blob Storage.
2. The `blob_trigger` function starts a **Durable Orchestration**.
3. The orchestrator:

   * Calls `analyze_pdf`: Extracts text using Form Recognizer.
   * Calls `summarize_text`: Summarizes the text using Azure OpenAI.
   * Calls `write_doc`: Saves the summary as a `.txt` in the `output` container.

---

## 🔧 Technologies Used

* Python (Azure Functions)
* Azure Durable Functions
* Azure Blob Storage
* Azure Form Recognizer
* Azure OpenAI Service
* Visual Studio Code

---

## �� Steps I Took

1. Created a new Azure Function App with the Durable Functions extension.
2. Created triggers and activity functions for:

   * Blob ingestion
   * Text extraction
   * OpenAI summarization
   * Output file upload
3. Used `DefaultAzureCredential` to authenticate without secrets.
4. Enabled **System Assigned Managed Identity** on the Function App.
5. Assigned the **Cognitive Services User** role on the Form Recognizer resource.
6. Switched from Python 3.13 to **Python 3.10** to resolve compatibility issues.
7. Recreated the virtual environment (`.venv`) using `python3.10 -m venv .venv`.
8. Installed required dependencies from `requirements.txt`.
9. Deployed to Azure directly from **VS Code** (Azure Extension → Function App → Deploy).
10. Uploaded test PDFs to validate the entire pipeline.

---

## ⚠️ Challenges Faced

* ❌ **ModuleNotFoundError**: `azure.storage.blob` was missing due to incompatible Python version.
* 🔄 VS Code defaulted to Python 3.13 — Azure Functions Core Tools only supports up to 3.10.
* 💡 Solution: Explicitly recreated `.venv` using Python 3.10 and reinstalled dependencies.
* 🧪 `DefaultAzureCredential` failed with `ClientAuthenticationError`:

  * Managed Identity was not recognized at first.
  * Fix: Enabled **System Assigned Identity** in Azure Portal.
  * Assigned the correct IAM role: **Cognitive Services User** to the Function App.
* ⏳ Azure VS Code extension got stuck when clicking the "+" to create a new app.

  * Fix: Refreshed the Azure extension tab to resolve it.

---

## How to Test

1. Upload a PDF file to the `input` container in your Azure Blob Storage account.
2. Trigger will invoke the orchestration function and process the document.
3. Processed summary will appear in the `output` container as a `.txt` file.
4. View logs in Azure Portal → Function App → "Monitor" → View Invocations.

---

## ▶️ How to Run Locally

1. Clone the repository:

   ```bash
   git clone https://github.com/YOUR_USERNAME/Intelligent-PDF-Summarizer.git
   cd Intelligent-PDF-Summarizer
   ```
2. Create and activate virtual environment with Python 3.10:

   ```bash
   python3.10 -m venv .venv
   source .venv/bin/activate  # or .venv\Scripts\activate on Windows
   ```
3. Install requirements:

   ```bash
   pip install -r requirements.txt
   ```
4. Set up your `local.settings.json` with the following:

   ```json
   {
     "IsEncrypted": false,
     "Values": {
       "AzureWebJobsStorage": "<your-blob-connection-string>",
       "BLOB_STORAGE_ENDPOINT": "<your-blob-connection-string>",
       "COGNITIVE_SERVICES_ENDPOINT": "<form-recognizer-endpoint>",
       "AZURE_OPENAI_ENDPOINT": "<openai-endpoint>",
       "AZURE_OPENAI_KEY": "<your-openai-key>",
       "CHAT_MODEL_DEPLOYMENT_NAME": "<deployment-name>",
       "FUNCTIONS_WORKER_RUNTIME": "python"
     }
   }
   ```
5. Start the function locally:

   ```bash
   func start --verbose
   ```
6. Upload a test PDF into the input container using Azure Storage Explorer or the Azure Portal.
7. Check local logs and output container for results.

---

## 🚀 How to Deploy to Azure Function App

1. Open the project in VS Code with the **Azure Functions** extension installed.
2. Make sure you're signed into your Azure account in VS Code.
3. In the Azure tab, click the ⚡ Functions icon, then click the **"Deploy to Function App"** button.
4. Select your subscription and create or choose an existing Function App.
5. When prompted, choose to overwrite files if deploying to an existing app.

### Add Required Configuration (Environment Variables)

Go to **Azure Portal** > Your Function App > **Configuration** > **Application Settings** tab. Add:

* `BLOB_STORAGE_ENDPOINT`
* `COGNITIVE_SERVICES_ENDPOINT`
* `AZURE_OPENAI_ENDPOINT`
* `AZURE_OPENAI_KEY`
* `CHAT_MODEL_DEPLOYMENT_NAME`

Then click **Save** and restart your Function App.

### Enable IAM + Managed Identity for Document Intelligence

1. In **Azure Portal**, go to your Function App.
2. Under "Identity" (in the left menu), enable **System Assigned** identity.
3. Go to your **Form Recognizer / Document Intelligence** resource.
4. Click **Access Control (IAM)** > **Add Role Assignment**.
5. Assign the role:

   * **Cognitive Services User**
   * Scope: This resource
   * Assign to: Your Function App's system identity
6. Click Save.

---


## 📁 Folder Structure

```
Intelligent-PDF-Summarizer/
│
├── function_app.py                 # Main function app with all triggers and activities
├── requirements.txt                # Dependencies
├── host.json                       # Function host configuration
├── local.settings.json             # Local environment settings (excluded in prod)
├── .venv/                          # Virtual environment
├── input/                          # Blob container (monitored)
└── output/                         # Blob container (output summaries)
```

---


## 🙌 Acknowledgments

- Azure Form Recognizer & OpenAI SDK Teams
- Microsoft Learn Durable Functions Docs
- Visual Studio Code Azure Tools
