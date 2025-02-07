
# Documenting the Cron Job Endpoint (`/api/cron`)

## Introduction

This document provides comprehensive information about the `/api/cron` endpoint, which allows developers to schedule tasks for their AI agents. It covers common use cases, implementation examples, security best practices, and troubleshooting tips. This endpoint is designed to execute background tasks at specified intervals, enabling automation and efficient resource management.

## Use Cases

The `/api/cron` endpoint can be used for a variety of tasks, including:

*   **Generating Daily Reports:** Automatically generate and send daily reports on agent performance, usage statistics, or other relevant metrics.
*   **Cleaning Up Old Data:** Regularly delete or archive old data to maintain database performance and reduce storage costs.
*   **Sending Scheduled Messages:** Schedule messages to be sent to users at specific times, such as reminders, notifications, or promotional offers.
*   **Model Retraining:** Periodically retrain AI models with new data to improve accuracy and performance.
*   **Data Synchronization:** Synchronize data between different systems or databases on a regular basis.
*   **Backup and Recovery:** Schedule regular backups of critical data to ensure data integrity and facilitate recovery in case of failures.

## Implementation Examples

To use the `/api/cron` endpoint, you need to configure a cron job that sends a request to the endpoint at the desired interval. The request must include the `CRON_SECRET` in the headers for authentication.

**Example Cron Expression:**

```
0 0 * * *  # Runs every day at midnight
*/5 * * * *  # Runs every 5 minutes
0 * * * *    # Runs every hour at the beginning of the hour
```

**Note:**  The specific cron expression syntax may vary depending on the operating system or cron scheduler being used.

### 1. Generating Daily Reports

This example demonstrates how to generate and send a daily report at midnight.

**Cron Job Configuration (Example using `crontab` on Linux):**

```
0 0 * * * curl -X POST https://your-api-endpoint.com/api/cron/generate_daily_report -H "X-Cron-Secret: YOUR_CRON_SECRET"
```

**Backend Implementation (Python/FastAPI Example):**

```python
from fastapi import FastAPI, Header, HTTPException
from typing import Optional
import datetime

app = FastAPI()

@app.post("/api/cron/generate_daily_report")
async def generate_daily_report(x_cron_secret: Optional[str] = Header(None)):
    """
    Generates and sends a daily report.
    """
    if x_cron_secret != "YOUR_CRON_SECRET":
        raise HTTPException(status_code=403, detail="Invalid Cron Secret")

    # Logic to generate the daily report
    report_data = {
        "date": datetime.date.today().isoformat(),
        "metrics": {
            "users_active": 100,
            "messages_sent": 500,
        }
    }

    # Logic to send the report (e.g., via email)
    print(f"Generating and sending daily report: {report_data}")
    # Replace with actual email sending logic
    return {"message": "Daily report generated and sent successfully"}
```

### 2. Cleaning Up Old Data

This example demonstrates how to clean up old data (e.g., logs) every week.

**Cron Job Configuration (Example using `crontab` on Linux):**

```
0 0 * * 0 curl -X POST https://your-api-endpoint.com/api/cron/cleanup_old_data -H "X-Cron-Secret: YOUR_CRON_SECRET"
```

**Backend Implementation (Python/FastAPI Example):**

```python
from fastapi import FastAPI, Header, HTTPException
from typing import Optional
import datetime

app = FastAPI()

@app.post("/api/cron/cleanup_old_data")
async def cleanup_old_data(x_cron_secret: Optional[str] = Header(None)):
    """
    Cleans up old data.
    """
    if x_cron_secret != "YOUR_CRON_SECRET":
        raise HTTPException(status_code=403, detail="Invalid Cron Secret")

    # Logic to identify and delete old data (e.g., logs older than 30 days)
    cutoff_date = datetime.date.today() - datetime.timedelta(days=30)
    print(f"Cleaning up data older than: {cutoff_date}")
    # Replace with actual database deletion logic
    return {"message": "Old data cleanup completed successfully"}
```

### 3. Sending Scheduled Messages

This example demonstrates how to send scheduled messages.  This example leverages the `/v1/messages/send/{accountId}` endpoint.

**Cron Job Configuration (Example using `crontab` on Linux - sending a message every day at 9 AM):**

```
0 9 * * * curl -X POST https://your-api-endpoint.com/api/cron/send_daily_reminder -H "X-Cron-Secret: YOUR_CRON_SECRET"
```

**Backend Implementation (Python/FastAPI Example):**

```python
from fastapi import FastAPI, Header, HTTPException
from typing import Optional
import datetime
import httpx  # For making HTTP requests

app = FastAPI()

@app.post("/api/cron/send_daily_reminder")
async def send_daily_reminder(x_cron_secret: Optional[str] = Header(None)):
    """
    Sends a daily reminder message.
    """
    if x_cron_secret != "YOUR_CRON_SECRET":
        raise HTTPException(status_code=403, detail="Invalid Cron Secret")

    # Define the message content and recipient
    message_data = {
        "content": {
            "message": "This is your daily reminder!",
            "type": "text"
        },
        "from": "YOUR_PHONE_NUMBER",  # Replace with your sender phone number
        "to": "RECIPIENT_PHONE_NUMBER",  # Replace with the recipient phone number
        "service": "whatsapp"  # Or "sms", "telegram", etc.
    }

    account_id = "YOUR_ACCOUNT_ID" # Replace with your account ID
    api_key = "YOUR_API_KEY" # Replace with your API Key
    api_secret = "YOUR_API_SECRET" # Replace with your API Secret

    # Make a request to the /v1/messages/send/{accountId} endpoint
    async with httpx.AsyncClient() as client:
        try:
            response = await client.post(
                f"https://your-api-endpoint.com/v1/messages/send/{account_id}",
                json=message_data,
                headers={
                    "x-api-key": api_key,
                    "x-api-secret": api_secret
                }
            )
            response.raise_for_status()  # Raise HTTPError for bad responses (4xx or 5xx)
            print(f"Message sent successfully: {response.json()}")
            return {"message": "Daily reminder sent successfully", "response": response.json()}
        except httpx.HTTPStatusError as e:
            print(f"Error sending message: {e}")
            raise HTTPException(status_code=500, detail=f"Error sending message: {e}")
        except Exception as e:
            print(f"An unexpected error occurred: {e}")
            raise HTTPException(status_code=500, detail=f"An unexpected error occurred: {e}")
```

**Important Considerations:**

*   **Error Handling:** Implement robust error handling to catch exceptions and log errors for debugging.
*   **Rate Limiting:** Be mindful of rate limits on external services (e.g., messaging platforms) and implement appropriate delays or retry mechanisms.
*   **Asynchronous Operations:** Use asynchronous operations (e.g., `asyncio` in Python) to avoid blocking the main thread and ensure responsiveness.

## Security Best Practices

Securing the `/api/cron` endpoint is crucial to prevent unauthorized access and potential abuse.

*   **`CRON_SECRET`:**
    *   **Strong Secret:** Use a strong, randomly generated secret for `CRON_SECRET`.  Avoid using easily guessable values.
    *   **Environment Variable:** Store the `CRON_SECRET` as an environment variable and never hardcode it in your application code.
    *   **Regular Rotation:**  Rotate the `CRON_SECRET` periodically to minimize the impact of potential compromises.
*   **HTTPS:** Always use HTTPS to encrypt communication between the cron scheduler and the `/api/cron` endpoint. This prevents eavesdropping and protects the `CRON_SECRET` from being intercepted.
*   **IP Whitelisting (Optional):** If possible, restrict access to the `/api/cron` endpoint to specific IP addresses or ranges associated with your cron scheduler.
*   **Rate Limiting:** Implement rate limiting to prevent abuse by limiting the number of requests that can be made to the endpoint within a given time period.
*   **Logging and Monitoring:**  Log all requests to the `/api/cron` endpoint and monitor for suspicious activity, such as unexpected request patterns or unauthorized access attempts.
*   **Input Validation:**  Validate any input data received by the endpoint to prevent injection attacks or other vulnerabilities.
*   **Least Privilege:**  Grant the cron job only the necessary permissions to perform its tasks. Avoid running cron jobs with root privileges unless absolutely necessary.

## Troubleshooting

*   **Cron Job Not Running:**
    *   **Check Cron Syntax:** Verify that the cron expression is correct and valid. Use a cron expression validator to ensure accuracy.
    *   **Check Cron Service:** Ensure that the cron service is running and enabled.
    *   **Check Logs:** Examine the cron logs (e.g., `/var/log/syslog` on Linux) for errors or warnings.
    *   **Permissions:** Verify that the user running the cron job has the necessary permissions to execute the command.
*   **Endpoint Not Receiving Requests:**
    *   **Check Network Connectivity:** Ensure that the cron scheduler can reach the API endpoint.
    *   **Check Firewall Rules:** Verify that there are no firewall rules blocking access to the endpoint.
    *   **Check API Endpoint:** Ensure that the API endpoint is running and accessible.
*   **Invalid Cron Secret:**
    *   **Verify Secret:** Double-check that the `CRON_SECRET` in the cron job configuration matches the value configured in your application.
    *   **Environment Variable:** Ensure that the `CRON_SECRET` environment variable is set correctly.
*   **Unexpected Behavior:**
    *   **Check Logs:** Examine the application logs for errors or exceptions.
    *   **Debug Code:** Use a debugger to step through the code and identify the source of the problem.
    *   **Test Thoroughly:** Test the cron job and the API endpoint thoroughly in a development environment before deploying to production.

By following these guidelines, you can effectively use the `/api/cron` endpoint to schedule tasks for your AI agents and ensure the security and reliability of your application.
