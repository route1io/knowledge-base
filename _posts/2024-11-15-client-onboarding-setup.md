---
title: "Client onboarding and setup"
date: 2024-11-15 07:48:00 -0400
---

This reference guide walks through how to setup and configure our environment for a new client onboarding, or to refresh settings for an existing client, either because of a brand update/refresh or other innovation

# CSS files

The admin tool allows for management of client-specific branding through the customization of css files.

To develop a new css file, log into the [admin tool]((https://route1io.shinyapps.io/admin/)) and select the css tab.

You can either load an existing css file for testing, delete remote css files, or change the settings and upload/save a new css.  The css file needs to be named as the client brand name that will be used across the product suite

![output reach frequency curve]({{site.url}}{{site.baseurl}}/images/admin/admin_css.jpg)

## Test Active Settings
To test the settings that have been chosen, press `test current settings`. The app will refresh with the chosen values.

Be sure to press the 'test success alert' and 'test error alert' buttons to preview popup dialog boxes


## Load Existing css file
To test an already existing css file, select it from the top dropdown and press `load existing css`

Note: the tool does not currently auto-populate the input boxes with values from the remote css

To find the settings please apply the remote css, run a hard refresh Ctrl-F5 and right click to inspect the webpage

The css file being used can be found named `test.css` in the Sources menu

Historic CSS files may have different settings & configutation to more recent versions and may need updating


## Upload a New css File
Once you are happy with the settings, enter a name for the css file in `css name` and then press `upload new css`

This will make the file available across all apps


## Delete Remote css File
If you need to delete a css file from the server, select it from the drop-down and press `delete remote css`

Please note, if that css file is used in other applications it will cause those applications to break

The css files are not actually deleted, but moved to a backup location that needs to be manually restored

They can be restored from the css/deleted s3 bucket



# Setting brand colors

The brand colors will be used by all apps in the product suite to ensure that charts are rendered using the client's brand colors. This makes it easy to put them into presentations or emails.

To set the brand colors, please build a `clientname.r` file with the below content and color definitions. This should be uploaded into the `brand_colors/` directory in the central s3 bucket.

Please ensure that more than 100 colors are available, this can be ensured by making use of the `rep()` command to cycle through color options

A range of colors should be used that will deliver clear distinction between each series in a chart. If necessary, please use chatgpt or equivalent with a query such as "given these existing brand colors please add 4 more colors that would fit the colors palette, and include orange, brown, purple and green options".

Care should be taken to order the colors in a visually distinct way. Yellow should be deprioritized on the list and used with caution as it often does not display well when presented on screens or in charts with a white background

{% highlight r %}

colors_custom=rep(c("#ce2222", "#22b5ce", "#22ceb5", "#ceb522", "#b522ce", "#444444", "#ff9900", "#ff0099", "#006633", "#660033"),100)


{% endhighlight %}


