---
title: "Client onboarding and setup"
date: 2024-11-15 07:48:00 -0400
---

This reference guide walks through how to setup and configure our environment for a new client onboarding, or to refresh settings for an existing client, either because of a brand update/refresh or other innovation

# Internal Systems Setup

## Set up time tracking codes in QuickBooks

1. Setting up the TSheets time codes is usually done by the Operations manager. 
2. Adding new clients/tasks (Admin only)
    1. Log into QuickBooks.
    2. Select 'Clients' in the left pane. This will open the "Manage Clients" pop-up screen. 
    3. To add a new client, click on the blue "+Add" button and enter the name of the Client you want to add.
    4. Once you have added the client, click into the entry and add the necessary folders and time codes according to the projects agreed with the client. 

# Brand Templates & setups

1. Collect the brand guide and a presentation (PowerPoint / Google Slides) template from the client.
2. Using the brand guide / presentation provided, create an CSS file for the client in the Admin Tool and a share a color palette for charting with the Data Scientist, to be added to the rscript as per the below. 

## CSS files

The admin tool allows for management of client-specific branding through the customization of css files.

To develop a new css file, log into the [admin tool]((https://route1io.shinyapps.io/admin/)) and select the css tab.

You can either load an existing css file for testing, delete remote css files, or change the settings and upload/save a new css.  The css file needs to be named as the client brand name that will be used across the product suite

![output reach frequency curve]({{site.url}}{{site.baseurl}}/images/admin/admin_css.jpg)

### Test Active Settings
To test the settings that have been chosen, press `test current settings`. The app will refresh with the chosen values.

Be sure to press the 'test success alert' and 'test error alert' buttons to preview popup dialog boxes


### Load Existing css file
To test an already existing css file, select it from the top dropdown and press `load existing css`

Note: the tool does not currently auto-populate the input boxes with values from the remote css

To find the settings please apply the remote css, run a hard refresh Ctrl-F5 and right click to inspect the webpage

The css file being used can be found named `test.css` in the Sources menu

Historic CSS files may have different settings & configutation to more recent versions and may need updating


### Upload a New css File
Once you are happy with the settings, enter a name for the css file in `css name` and then press `upload new css`

This will make the file available across all apps


### Delete Remote css File
If you need to delete a css file from the server, select it from the drop-down and press `delete remote css`

Please note, if that css file is used in other applications it will cause those applications to break

The css files are not actually deleted, but moved to a backup location that needs to be manually restored

They can be restored from the css/deleted s3 bucket


## Setting Brand Colors

The brand colors will be used by all apps in the product suite to ensure that charts are rendered using the client's brand colors. This makes it easy to put them into presentations or emails.

To set the brand colors, please build a `clientname.r` file with the below content and color definitions. This should be uploaded into the `brand_colors/` directory in the central s3 bucket.

Please ensure that more than 100 colors are available, this can be ensured by making use of the `rep()` command to cycle through color options

A range of colors should be used that will deliver clear distinction between each series in a chart. If necessary, please use chatgpt or equivalent with a query such as "given these existing brand colors please add 4 more colors that would fit the color palette, and include orange, brown, purple and green options".

Care should be taken to order the colors in a visually distinct way. Yellow should be deprioritized on the list and used with caution as it often does not display well when presented on screens or in charts with a white background

{% highlight r %}

colors_custom=rep(c("#ce2222", "#22b5ce", "#22ceb5", "#ceb522", "#b522ce", "#444444", "#ff9900", "#ff0099", "#006633", "#660033"),100)


{% endhighlight %}


# Document Storage Locations

## Locations and setups to share with the internal project members:

1. Set up an internal SharePoint site by following these steps: 
    1. In Microsoft 365, navigate to the SharePoint admin page
    2. Create a new Team site.
    3. Select a site name (the Client's name).
    4. Set a site owner.
    5. Add additional site members - all internal members that will be working with the Client.
    6. Navigate to the new SharePoint site and change any permissions necessary
2. Set up a GitHub Repo for the Client.
3. Set up the architecture for Central process for the Client.

## Locations to share with the internal project members and stakeholders:

1. Set up a Shared Google Drive (if applicable).
    1. In Google Drive, navigate to "Shared drives" in the left pane.
    2. Click on the "+ New" in the top left corner. 
    3. Enter the name for the Shared Drive (this should follow the format of route1.io:client_name)
    4. Add additional members to the Shared Drive - all internal members that will be working with the Client as well as the Client team and possibly their Media Agency.

2. Set up an s3 bucket for the Client. 

# Asana Project Management

Please note: The templates for the different types of Asana boards are available in the Asana Template library and will only be accessible to Administrators of Asana. 

1. Set up all the required Asana boards. 
    1. This could include the following boards:
        1. Support Board
        2. Data Collection Board
        3. Model Development Board
        4. Geo-lift Test Tracking and Planning Board  
2. Add all of the Client's Asana boards to a portfolio for the Client. 
3. Share the Asana boards and portfolio with the relevant stakeholders, who could also include members from the Client's project team. 
4. Train the Client on how to use Asana, if they will be actively using Asana. 
    1. Asana training resources are also available here: 
    2. [route1.io Asana Basics Training]((https://route1io0.sharepoint.com/:v:/s/Marketing/EYG6cDL6BxJAu0hToD62noIBvJuI36wjCr_fwa2U6rVbvg?e=bczKOn))
    3. [route1.io Asana Data Collection Training]((https://route1io0.sharepoint.com/:v:/s/Marketing/ERuM09QAJCVFkrC04dA46JcBGZw6Fd2YZWwzv4luHYmvTw?e=wXe710))
    
5. Ensure that each project board has all the necessary information included on the overview page:  
    1. Add links to locations in the Key Resources section of the project board
    2. Add the project brief
    3. Add project milestones
    4. Add tasks and subtasks
    5. Assign tasks (only of these have not already been automatically assigned by the template)
