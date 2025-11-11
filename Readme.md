# Serverless AI Chatbot with Amazon Bedrock, Lambda, API Gateway & S3 🤖

## Project Banner
**Serverless AI Chatbot using Amazon Bedrock**

Build this hands-on demo step by step with my detailed tutorial on **Julien Muke YouTube**. Feel free to subscribe 🔔!

### 🚨 Tutorial
This repository contains the steps corresponding to an in-depth tutorial available on my YouTube channel, **Julien Muke**.

If you prefer visual learning, this is the perfect resource for you. Follow my tutorial to learn how to build projects like these step-by-step in a beginner-friendly manner!

## 🤖 Introduction
In this step-by-step tutorial, you'll learn how to create a powerful serverless AI Chatbot using Amazon Bedrock's **Titan Text G1 - Express LLM**. We’ll connect it to **AWS Lambda**, expose it via **API Gateway** with proper CORS handling, and deploy a beautiful HTML/JavaScript frontend using **S3 static website hosting**.

## 🔎 Overview
- Users interact with the chatbot either through a static frontend hosted on **Amazon S3** or **Amplify Hosting** or by calling the API directly.
- Their messages are sent as HTTPS requests to **Amazon API Gateway**, which securely forwards them to an **AWS Lambda** function.
- The **Lambda** function processes the input and uses an **IAM Role** to securely call **Amazon Bedrock**, invoking the **Titan model** to generate a chatbot response.
- That response flows back through **Lambda** and **API Gateway** to the user.

## 🛠 Tech Stack:
- **Amazon Bedrock** (Titan Text G1 - Express)
- **AWS Lambda**
- **Amazon API Gateway**
- **Amazon S3** (Static Hosting)
- **JavaScript** + **HTML** + **CSS**

---

## ➡️ Step 1 - Set Up Amazon Bedrock Access
Make sure your AWS account has **Bedrock access** (Bedrock is GA now, but some regions might differ — **N. Virginia (us-east-1)** is safest).

1. Go to the **AWS Console** → **Amazon Bedrock**
2. Request access to `amazon.titan-text-express-v1`. You can also choose any models you want to use based on your needs.
   
   **Once approved**, you're ready to build!

---

## ➡️ Step 2 - Create an IAM Role for Lambda
To create an **IAM role** with the console:

1. In the navigation pane, search for **IAM**, choose **Roles**, and then choose **Create role**.
2. For **Trusted entity type**, choose **AWS service**.
3. For **Service or use case**, choose **Lambda** then Choose **Next**.
4. For **Permissions policies**, select:
   - **AmazonBedrockFullAccess**
   - **CloudWatchLogsFullAccess**
5. For **Role name**, enter `chatBotLambdaRole`.
6. Review the role and then choose **Create role**.

<details>
<summary><code>lambda_function.py</code></summary>

---

## ➡️ Step 3 - Create the Lambda Function
To create a **Lambda function** with the console:

1. In the navigation pane, search for **Lambda function** and choose **Create function**.
2. Select **Author from scratch**.
3. In the **Basic information** pane, for **Function name**, enter `chatbotlambda`.
4. For **Runtime**, choose **Python 3.12** (easiest for Bedrock SDK usage).
5. Leave **architecture** set to `x86_64`, then choose **Create function**.

For the Lambda function code, copy and paste the code below into your Lambda code editor:

```python
# lambda_function.py

import boto3

def lambda_handler(event, context):
    message = event['body']
    # Instantiate a client to call Bedrock
    bedrock_client = boto3.client('bedrock-runtime', region_name='us-east-1')
    response = bedrock_client.invoke_model(
        modelId='amazon.titan-text-express-v1',
        body=message
    )
    return {
        'statusCode': 200,
        'body': response['body'].read().decode('utf-8')
    }
