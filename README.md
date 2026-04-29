# Serverless Web Hosting with AWS Lambda & API Gateway

## Overview
A lightweight public-facing webpage served entirely via AWS Lambda and 
API Gateway — no servers or VMs required.

## Architecture
Browser → API Gateway (HTTP API) → AWS Lambda → Returns HTML

## Tech Stack
- AWS Lambda (Python 3.x)
- Amazon API Gateway (HTTP API)
- Python

## Features
- Serverless — zero server management
- Auto-scaling by default
- Public URL via API Gateway Invoke URL
- Custom route: GET /home

## Screenshots
![Lambda Function](screenshots/lambda-function.png)
![Deployed Code](screenshots/deployed-code.png)
![Browser Output](screenshots/browser-output.png)

## What I Learned
- How to serve HTML directly from a Lambda function
- How to expose Lambda via API Gateway HTTP API
- Serverless architecture fundamentals on AWS
