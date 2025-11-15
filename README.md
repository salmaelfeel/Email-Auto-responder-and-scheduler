Sure! Here’s a **clean version of the README.md** **without** code snippets for direct copying, just with descriptions and steps for you to customize:

---

# **Email Auto-Responder & Scheduler**

This project automates email classification, auto-reply for meeting requests, and email scheduling. It uses **n8n** for workflow automation and **OpenRouter (Z.ai GLM 4.5 Air)** for language model processing.

---

## 1. **Setup Instructions**

### **Prerequisites:**

* **Docker** and **Docker Compose** installed on your system.

### **Steps to Run Locally:**

1. Clone this repository or copy the project files to your local machine.

2. In your project folder, create a `.env` file using `.env.example` as a template.

3. Edit the `.env` file with your **Google OAuth credentials** and other necessary configuration (described in the OAuth section below).

4. To start the project, run the following command:

   * `docker-compose up -d`

5. Visit **[http://localhost:5678](http://localhost:5678)** in your browser to access n8n's interface.

---

## 2. **OAuth Steps for Google Services**

To enable **Gmail**, **Google Calendar**, and **Google Sheets** access, follow these steps:

1. Go to the [Google Cloud Console](https://console.cloud.google.com/).

2. **Create a new project** (if you haven't already).

3. **Enable the APIs**:

   * Gmail API
   * Google Calendar API
   * Google Sheets API

4. **Create OAuth credentials**:

   * Go to **Credentials** → **Create Credentials** → **OAuth 2.0 Client IDs**.
   * Set the **redirect URI** to:
     `http://localhost:5678/rest/oauth2-credential/callback`
   * Download the **JSON credentials** file.

5. **Configure OAuth credentials in n8n**:

   * Open your `.env` file.
   * Add the following keys from the downloaded credentials file:

     * `GOOGLE_CLIENT_ID`
     * `GOOGLE_CLIENT_SECRET`

6. **OAuth setup in n8n**:

   * When you first run n8n, you will be prompted to authenticate with Google. This will allow n8n to access Gmail, Calendar, and Sheets using your credentials.

---

## 3. **Test Cases**

### **Short Examples of Each Intent:**

Meeting Request:

Input: "Can we meet tomorrow at 2 PM UTC?"

Action: The AI model classifies the email as a "Meeting Request". It checks availability in Google Calendar, and if the time is available, it schedules the meeting and sends a confirmation reply.

General Inquiry / Support Request / Spam / Out-of-Office:

Input: (General Inquiry) "Can you help me with my account settings?"
(Support Request) "I need assistance with my billing issue."
(Spam/Marketing) "Get 50% off your next purchase!"
(Out-of-Office) "I'm currently out of the office."

Action:

The email is logged to Google Sheets.

The email is marked as read.

No reply is sent, as the email is not classified as a "Meeting Request".

These combine all the non-meeting intents into one action. For simplicity, the system logs the email and marks it as read without any further action. The Meeting Request intent triggers the full scheduling and replying process.

---

## 4. **Troubleshooting**

### **Common Issues:**

1. **Model Not Responding:**

   * **Cause**: The email may lack key details such as the **date**, **time**, or **time zone**.
   * **Solution**: Ensure the email includes a clear meeting request with **specific date**, **time**, and **time zone**.

2. **Google OAuth Issues:**

   * **Cause**: Incorrect OAuth credentials or redirect URI.
   * **Solution**: Double-check the credentials in your `.env` file and ensure that the redirect URI is correctly set in both your Google Cloud Console and n8n.

3. **OpenRouter Request Rate Limit:**

   * **Cause**: The OpenRouter model you're using (`z-ai/glm-4.5-air:free`) has a **rate limit** for requests.
   * **Solution**: If you hit the rate limit, try again after a short period. Consider upgrading to a higher tier for increased limits.

4. **Emails Not Being Processed Correctly:**

   * **Cause**: Missing or malformed email content.
   * **Solution**: Ensure the emails are formatted properly, especially those intended for scheduling. The model needs clear intent and relevant details.

---

## 5. **Folder Structure**

```
n8n-email-automation/
│
├── workflows/
│   ├── email-auto-responder.json
│   ├── scheduler.json
│
├── templates/
│   ├── appointment_reply.md
│   ├── follow_up_request.md
│   └── non_appointment_reply.md
│
├── config/
│   └── .env.example
│
├── logs/
│   └── audit_log.csv
│
├── docker-compose.yml
└── README.md
```

---

## 6. **Additional Notes**

* **OpenRouter Model**: The free tier of **Z.ai GLM 4.5 Air** has a **request rate limit**. Be mindful of this during heavy usage.
* The **n8n instance** is local by default. To deploy it to a production environment, you can follow Docker deployment instructions, or use services like **AWS** or **DigitalOcean** for hosting.
