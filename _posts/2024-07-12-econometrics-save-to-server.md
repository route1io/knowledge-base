---
title: "Econometrics App: Setting up Cloud Based Model Storage"
date: 2024-05-31 22:48:00 -0400
---

The econometric modeling app accesses model data files that are stored both locally within the app's data directory, and can connect to a central AWS s3 bucket to read, display and save models to the cloud.

# s3 Location

The s3 location for cloud based model file storage is **_route1io.econometrics_** and within this application the model should be saved under the _clientname_ top-level folder to ensure that client-level user access controls function correctly.

![s3 location: route1io.econometrics]({{site.url}}{{site.baseurl}}/images/econometrics/econometrics_cloud_s3_location.jpg)

With the model data files uploaded to the correct s3 bucket, users will be able to access them through the _\cloud_ directory in the load-model dialog

![econometrics load dialog]({{site.url}}{{site.baseurl}}/images/econometrics/econometrics_load_models.jpg)
