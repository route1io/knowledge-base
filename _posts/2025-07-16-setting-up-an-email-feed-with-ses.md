---
title: "Setting up a scalable email data feed with SES and Lambda"
date: 2025-07-16 22:48:00 -0400
---

# Building a Serverless Email Ingestion Pipeline with AWS SES, Lambda, and S3

In many modern data workflows, clients still deliver data via email—often daily attachments from tools like Looker or Tableau. While not ideal, it's common enough to warrant a proper solution. In this post, we’ll walk through a scalable, serverless pipeline using **AWS SES**, **Lambda**, and **S3** to ingest email attachments into cloud storage.

---

## 🔧 Why We Built This

We needed a way to:
- Receive data from clients via email.
- Automatically extract file attachments (CSV, XLSX, etc.).
- Store them in a structured, queryable way in S3.
- Enable future processing for building a master dataset.

We didn’t want to expose personal work emails or rely on fragile workarounds.

---

## 🔐 Step 1: Create a Dedicated Subdomain

We created a subdomain just for ingestion:

```
ingest.example.com
```

This lets us route only those emails we intend to process, separate from internal or normal company mail.

### ✅ DNS Setup

To hook this up to Amazon SES (Simple Email Service), we first have to verify our domain and setup MX records:

- **MX Record**:
  Points to SES' inbound mail server
  ```
  inbound-smtp.<region>.amazonaws.com
  ```

- **CNAME Records**:
  Verifies the subdomain with SES

This ensures only emails sent to `@ingest.example.com` get routed into our processing pipeline.

See [Creating a domain identity](https://docs.aws.amazon.com/ses/latest/dg/creating-identities.html#verify-domain-procedure) and [Publishing an MX record for Amazon SES email receiving](https://docs.aws.amazon.com/ses/latest/dg/receiving-email-mx-record.html) from the AWS docs for more details.

---

## 📬 Step 2: Set Up Amazon SES Inbound Rules

With the domain verified, we created an **Inbound Rule Set** and made it active.

### 🧩 Each Rule:
- Matches an email recipient like `a@ingest.example.com`
- Executes 2 actions:
  1. Save the full raw email to S3 in a designated folder using [S3Action](https://docs.aws.amazon.com/ses/latest/APIReference/API_S3Action.html).
  2. Invoke a Lambda function for processing via [LambdaAction](https://docs.aws.amazon.com/ses/latest/APIReference/API_LambdaAction.html).

This structure makes it easy to scale across many clients later.

---

## Step 3: Store Raw Emails in S3

An example procedure is to have Amazon SES store the email as a `.eml` file in our designated S3 bucket under a prefix:

```
s3://your-bucket/email/client-a/
```

This gives us a reliable source-of-truth for every email received.

---

## 🧠 Step 4: Trigger a Lambda to Process the Email

Once we have our raw emails saved via the S3Action, we can now trigger a Lambda to process the data wherever we need it (for example an attachments/ folder in the same S3 bucket.)

The Lambda function does the heavy lifting:
- Reads the `.eml` file from S3
- Parses the email
- Extracts all attachments (CSV, XLSX, etc.)
- Saves each attachment to:

```
s3://your-bucket/attachments/client-a/
```

This gives us clean, ready-to-use files with no human effort.

### 📓 Logging
All Lambda logs stream to CloudWatch for visibility and debugging.

### 🔐 Permissions
The Lambda IAM role requires:
- `s3:GetObject` (to read the email)
- `s3:PutObject` (to write attachments)

---

## 🧼 Folder Structure

We structured S3 for per-client isolation:

```
/email/client-a/        <- Raw emails (.eml)
/attachments/client-a/  <- Extracted files
```

This enables organized, scalable ingestion across dozens of clients without mixing data.

---

## ✅ Outcome

This setup is now:

- Serverless
- Scalable
- Easy to monitor and expand
- Cleanly separated by client
- Durable for long-term storage and future processing

---

## 🧠 What's Next?

With data now flowing reliably into S3, the next step will be processing or transforming those files into a master dataset.
