**AWS Serverless Web Hosting with Lambda and API Gateway** — serving a public webpage without a single server or VM.

---

## Project at a Glance

| | |
|---|---|
| **Tools Used** | AWS Lambda, Amazon API Gateway (HTTP API), AWS Console |
| **Platform** | Amazon Web Services (AWS) |
| **Languages** | Python 3.x |
| **What It Does** | Deploys a public-facing webpage served entirely through a Lambda function invoked via an API Gateway HTTP API endpoint |

---

## The Problem This Project Solves

Traditional web hosting requires provisioning servers, managing operating systems, patching runtimes, and paying for compute time even when no traffic is coming in. For lightweight workloads — a landing page, a status endpoint, a simple form handler — this overhead is unnecessary and expensive. The infrastructure becomes a liability rather than an asset.

Serverless computing flips this model entirely. AWS Lambda executes code only in response to events, and Amazon API Gateway acts as the front door, routing HTTP requests directly to the function. The result is a web endpoint that costs nothing at rest, scales automatically under load, and requires no server management whatsoever.

This project demonstrates the full end-to-end setup of that pattern: writing an HTML response directly inside a Python Lambda function, configuring an HTTP API in API Gateway, mapping a GET route to the function, and verifying the result in a browser using the generated invoke URL. It is a foundational pattern that underpins modern microservices, webhook receivers, and lightweight APIs deployed at scale on AWS.

---

## Architecture

```
User's Browser
      |
      | HTTPS GET /home
      v
+---------------------+
| Amazon API Gateway  |
|   (HTTP API)        |
|   Route: GET /home  |
+---------------------+
      |
      | Lambda Proxy Integration
      v
+---------------------+
|   AWS Lambda        |
|   Runtime: Python   |
|   Returns: HTML     |
+---------------------+
      |
      | HTTP 200 + HTML body
      v
User's Browser renders the page
```

API Gateway handles TLS termination, request routing, and throttling. Lambda receives the event object, constructs an HTTP response with the appropriate headers and HTML body, and returns it. No load balancer, no web server process, no EC2 instance.

---

## Repository Structure

```
aws-serverless-web-lambda/
├── README.md
├── browser-output.png        # Screenshot: rendered webpage in browser
├── deployed-code.png         # Screenshot: Lambda function code in AWS Console
└── lambda-function.png       # Screenshot: Lambda function configuration
```

---

## Walkthrough

### Step 1: Create the Lambda Function

In the AWS Console, navigate to Lambda and create a new function with the Python 3.x runtime. The function returns a well-formed HTTP response object with status code 200 and an HTML body:

```python
def lambda_handler(event, context):
    return {
        'statusCode': 200,
        'headers': {
            'Content-Type': 'text/html'
        },
        'body': '''
            <html>
              <body>
                <h1>Hello from AWS Lambda</h1>
                <p>This page is served entirely serverlessly.</p>
              </body>
            </html>
        '''
    }
```

### Step 2: Create the API Gateway HTTP API

In API Gateway, create an HTTP API. Add a route: `GET /home`. Attach a Lambda integration pointing to the function created in Step 1. Enable Lambda proxy integration so the full request and response objects are passed through.

### Step 3: Deploy the API

Create a stage (e.g., `$default`) and deploy. API Gateway generates an invoke URL in the form:

```
https://<api-id>.execute-api.<region>.amazonaws.com/home
```

### Step 4: Verify in Browser

Open the invoke URL in a browser. The HTML page renders, confirming the full request path from the public internet through API Gateway to Lambda and back.

---

## How to Reproduce

Prerequisites: An AWS account with permissions to create Lambda functions and API Gateway resources.

**1. Create the Lambda function**

```bash
# In the AWS Console: Lambda > Create function
# Name: serverless-web-page
# Runtime: Python 3.x
# Paste the lambda_handler code above and click Deploy
```

**2. Create the HTTP API**

```bash
# In the AWS Console: API Gateway > Create API > HTTP API
# Add integration: Lambda > select your function
# Add route: GET /home
# Deploy to stage: $default
```

**3. Test the endpoint**

```bash
# Copy the Invoke URL from the API Gateway console
curl https://<your-api-id>.execute-api.<region>.amazonaws.com/home
```

**4. Open in browser**

Navigate to the invoke URL. Verify the HTML page is rendered correctly.

---

## Key Observations

- The Lambda function cold-start time is negligible for a Python function of this size.
- API Gateway HTTP APIs are faster and cheaper than REST APIs for simple proxy integrations.
- The function scales from 0 to thousands of concurrent executions automatically — no capacity planning required.
- Cost at this scale is effectively zero: AWS Free Tier covers 1 million Lambda invocations per month.

---

*Oluwaseyi Michael Falode · Cybersecurity & Cloud Security Engineer · Toronto, ON*
