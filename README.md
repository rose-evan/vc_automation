# VC Deal Flow Automation

An intelligent automation system that reads unread emails, parses attachments (especially pitch decks), uses AI to extract and summarize company information, and automatically adds companies to your Airtable deal flow database.

## 🚀 Features

- **Email Processing**: Automatically reads unread emails from IMAP servers (Gmail, Outlook, etc.)
- **PDF Parsing**: Extracts text from pitch decks and other PDF attachments using multiple parsing engines
- **AI-Powered Analysis**: Uses OpenAI GPT-4 to extract structured company information and generate summaries
- **Airtable Integration**: Automatically creates or updates records in your Airtable base
- **Intelligent Categorization**: Automatically categorizes companies by sector and funding stage
- **Comprehensive Logging**: Detailed logging for monitoring and debugging

## 📋 Prerequisites

- Python 3.8+
- OpenAI API key
- Airtable API key and base ID
- Email account with IMAP access

## 🛠️ Installation

1. **Clone the repository**:
   ```bash
   git clone <your-repo-url>
   cd vc_automation
   ```

2. **Create a virtual environment**:
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # On Windows: .venv\Scripts\activate
   ```

3. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

4. **Set up configuration**:
   ```bash
   python main.py
   ```
   This will create a `config.json` file that you need to fill with your credentials.

## ⚙️ Configuration

Edit the `config.json` file with your actual credentials:

```json
{
  "email": {
    "use_gmail_api": true,
    "credentials_file": "credentials.json",
    "token_file": "token.json"
  },
  "ai": {
    "api_key": "your-openai-api-key",
    "model": "gpt-4",
    "max_tokens": 1000
  },
  "airtable": {
    "api_key": "your-airtable-api-key",
    "base_id": "your-base-id",
    "table_name": "Deal Flow"
  },
  "processing": {
    "max_emails_per_run": 10,
    "supported_attachments": [".pdf", ".docx", ".doc"],
    "keywords": ["pitch deck", "startup", "funding", "investment", "demo"]
  }
}
```

### Getting Your Credentials

#### Email Setup (Gmail Example)
1. Enable 2-factor authentication on your Gmail account
2. Generate an App Password: Google Account → Security → App Passwords
3. Use the app password in the config (not your regular password)

#### OpenAI API Key
1. Sign up at [OpenAI](https://platform.openai.com/)
2. Create an API key in your account settings
3. Add the key to your config

#### Airtable Setup
1. Create an Airtable base for your deal flow
2. Get your API key from [Airtable Account](https://airtable.com/account)
3. Get your base ID from the API documentation URL
4. Create a table with the following recommended fields:
   - Company Name (Single line text)
   - Sector (Single select)
   - Funding Stage (Single select)
   - Traction (Long text)
   - Summary (Long text)
   - Email Subject (Single line text)
   - Email Sender (Single line text)
   - Processed Date (Date)

## 🔑 Gmail API Setup (OAuth)

To use the Gmail API (recommended for security and reliability):

1. Go to the [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or select an existing one)
3. Enable the Gmail API for your project
4. Go to "APIs & Services > Credentials"
5. Click "Create Credentials" > "OAuth client ID"
6. Choose "Desktop app" and download the `credentials.json` file
7. Place `credentials.json` in your project root
8. The first time you run the script, a browser window will open for you to log in and authorize access. This will create a `token.json` file for future use.

**Config Example for Gmail API:**
```json
{
  "email": {
    "use_gmail_api": true,
    "credentials_file": "credentials.json",
    "token_file": "token.json"
  },
  ...
}
```

If you want to use IMAP instead, set `use_gmail_api` to `false` and provide IMAP credentials as before.

## 🤖 Gemini API (Vertex AI) Setup

To use Google Gemini (Vertex AI Generative Language API):

1. Go to the [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or select an existing one)
3. Enable the Vertex AI API for your project
4. Set up billing if required
5. Create a service account with the Vertex AI User role
6. Download the service account JSON key and place it in your project root (e.g., `service_account.json`)
7. Set the environment variable for authentication:
   ```bash
   export GOOGLE_APPLICATION_CREDENTIALS="/path/to/service_account.json"
   ```
8. (Optional) Install the SDK:
   ```bash
   pip install google-cloud-aiplatform
   ```

**Config Example for Gemini API:**
```json
{
  "ai": {
    "provider": "gemini",
    "project_id": "your-gcp-project-id",
    "location": "us-central1",
    "model": "gemini-1.0-pro",
    "max_tokens": 1000
  },
  ...
}
```

- `project_id`: Your Google Cloud project ID
- `location`: Region for Vertex AI (e.g., `us-central1`)
- `model`: Gemini model name (e.g., `gemini-1.0-pro`)

## 🏃‍♂️ Usage

### Basic Usage
```bash
python main.py
```

### Scheduled Execution
Set up a cron job or use a task scheduler to run the script periodically:

```bash
# Run every hour
0 * * * * cd /path/to/vc_automation && python main.py

# Run every 30 minutes
*/30 * * * * cd /path/to/vc_automation && python main.py
```

### Programmatic Usage
```python
from main import VCAutomation

# Initialize the automation
automation = VCAutomation()

# Run the full pipeline
automation.run()

# Or run individual components
companies = automation.process_emails()
automation.upload_to_airtable(companies)
```

## 📁 Project Structure

```
vc_automation/
├── main.py              # Main orchestrator script
├── email_reader.py      # Email processing and IMAP handling
├── pdf_parser.py        # PDF text extraction and parsing
├── summarizer.py        # AI-powered analysis and summarization
├── airtable_uploader.py # Airtable integration
├── config.json          # Configuration file (created automatically)
├── requirements.txt     # Python dependencies
├── README.md           # This file
└── vc_automation.log   # Log file (created automatically)
```

## 🔧 Customization

### Adding New Email Providers
The system supports any IMAP server. Update the email configuration:

```json
{
  "email": {
    "imap_server": "outlook.office365.com",  // For Outlook
    "imap_port": 993,
    "email": "your-email@outlook.com",
    "password": "your-password"
  }
}
```

### Customizing AI Prompts
Edit the prompts in `summarizer.py` to extract different information or change the analysis style.

### Modifying Airtable Fields
Update the field mapping in `airtable_uploader.py` to match your Airtable schema.

### Adding New File Types
Extend the attachment processing in `main.py` to handle additional file types.

## 📊 What Information is Extracted

The AI extracts the following information from emails and attachments:

- **Company Name**: Full company name
- **Sector**: Primary industry (AI, Fintech, Healthtech, etc.)
- **Funding Stage**: Current stage (Pre-seed, Seed, Series A, etc.)
- **Traction**: Key metrics (users, revenue, growth)
- **Team Size**: Number of employees
- **Location**: Company location
- **Valuation**: Current valuation
- **Funding Amount**: Amount being raised
- **Use of Funds**: How funding will be used
- **Key Metrics**: Business metrics (ARR, MRR, CAC, etc.)
- **Competitive Advantage**: Key differentiators
- **Market Size**: Target market (TAM, SAM, SOM)
- **Business Model**: Revenue model
- **Technology**: Technical differentiators
- **Summary**: AI-generated company summary

## 🐛 Troubleshooting

### Common Issues

1. **Email Connection Failed**
   - Check your email credentials
   - Ensure IMAP is enabled
   - For Gmail, use an App Password

2. **PDF Parsing Issues**
   - Install additional PDF libraries: `pip install PyMuPDF pdfplumber`
   - Check if PDFs are password-protected

3. **OpenAI API Errors**
   - Verify your API key
   - Check your OpenAI account balance
   - Ensure you have access to GPT-4

4. **Airtable Upload Failures**
   - Verify API key and base ID
   - Check field names match your Airtable schema
   - Ensure you have write permissions

### Debug Mode
Enable detailed logging by modifying the logging level in `main.py`:

```python
logging.basicConfig(level=logging.DEBUG)
```

## 🔒 Security Considerations

- Store API keys securely (consider using environment variables)
- Use app passwords for email accounts
- Regularly rotate API keys
- Monitor API usage to control costs

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🆘 Support

For issues and questions:
1. Check the troubleshooting section
2. Review the logs in `vc_automation.log`
3. Open an issue on GitHub

## 🎯 Future Enhancements

- Web interface for monitoring and configuration
- Support for more file types (Word docs, Excel sheets)
- Integration with CRM systems
- Advanced filtering and scoring algorithms
- Email response automation
- Deal flow analytics and reporting