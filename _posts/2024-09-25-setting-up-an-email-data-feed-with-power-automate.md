---
title: "Video Plan Simulation"
date: 2024-09-25 07:48:00 -0400
---

# Setting up an email data feed with Power Automate

Power Automate can streamline workflows by automatically handling email attachments and saving them to OneDrive. In this guide, we'll walk through a process that extracts CSV files from incoming emails, creates a timestamped folder, and stores the files within a SharePoint Site’s associated OneDrive.

## What is Power Automate?

[Power Automate](https://www.microsoft.com/en-us/power-platform/products/power-automate) is a cloud-based tool from Microsoft that automates workflows between apps and services, enabling us to sync files, receive notifications, and manage data without manual intervention.

As part of the Microsoft 365 ecosystem, it integrates seamlessly with tools like OneDrive, SharePoint, Outlook, and Excel.

In this guide, we’ll leverage Power Automate to streamline the process of moving CSV attachments from incoming emails to OneDrive.

## Step-by-step: Creating a Power Automate workflow

Now let's jump in and create our Power Automate workflow in the platform

1. Log in to Power Automate:

- Navigate to [Power Automate](https://www.microsoft.com/en-us/power-platform/products/power-automate) and log in with your Microsoft 365 credentials.

2. Create a new flow:

- On the left-hand sidebar, click on "Create".
- You’ll be presented with several options. Select "Automated cloud flow" since we’re triggering the workflow when a new email arrives

3. Name your flow and choose a trigger:

- In the dialog box that appears, give your flow a name, such as "Move CSV Attachments to OneDrive".
- Under Choose your flow's trigger, search for the trigger titled "When a new email arrives (V3)".
- Click "Create" to proceed.

4. Set email filter conditions (optional):

- Once the trigger is added, you can narrow down the emails that trigger this flow:
  - In the trigger step, you can add filters such as Inbox, From address, Subject line, specific Folder, or Has Attachments to refine the emails that the workflow will act upon.
- For instance, you might specify a Subject Filter containing "SA360 export" to only trigger on emails that have "SA360 export" in the subject line

5. Initialize variables for metadata:

- Click the "New Step" button below the trigger.
- Search for "Initialize variable" in the action search box.
- Add relevant variables that will aid in the control flow one by one i.e.:
  - Initialize Variable: Name it timestamp and set the type to String. Use the "Expression" function to insert a dynamic timestamp (e.g., utcNow()).
  - Initialize Variable 2: Name it fileExtension and set the type to String.
  - Initialize Variable 3: Name it fileName and set the type to String.
- These variables will be populated later in the flow, helping control file naming and organization.