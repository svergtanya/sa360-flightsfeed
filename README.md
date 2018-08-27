# Flights Feed

An inventory feed generator for airlines. Enables building, configuring and
connecting route based inventory data in multiple languages into Doubleclick
search. Inventory data generated by Flights feed enables advertisers to automate
the creation of thousands of route based inventory campaigns, ad groups, and ad
copies. The inventory feed can also be used to update business data on
Doubleclick search.

More information on [inventory management in Doubleclick
search](https://support.google.com/ds/answer/7129110?hl=en).

## Overview

This is an [Google Apps Script](https://developers.google.com/apps-script/),
[Google Big Query](https://cloud.google.com/bigquery/) based tool. An Apps
Script is hosted on your Google Drive and orchestrates the process of
multiplying and joining airline data sources via [Big
Query](https://cloud.google.com/bigquery/).

### How it works

1.  Apps Script picks up statically defined data (in Google Sheets) around
    enabled airline routes, country and city translations, landing page URLs
    etc.. and copies that data into BQ tables.
2.  The apps script sets up query views for processing the data and triggers the
    BQ views to run asynchronously in sequence.
3.  The output of the final view gets exported as a CSV file into Google Cloud
    Storage.
4.  The feed generator script can be scheduled to run in Apps Script based on
    triggers, such as daily every hour. The script can be extended to listen to
    changes on the source sheet by the user to trigger an update on the BQ
    tables (data copy) and execute the BQ views.
5.  Once the final feed CSV file is exported to Google Cloud Storage, contact
    Doubleclick's support to help you connect it into your Doubleclick search
    account.

## Setup instructions

-   Create a new
    [Google Cloud Platform Project](https://cloud.google.com/resource-manager/docs/creating-managing-projects)
    save the project ID generated in the cloud console for later configuration.

-   Enable the following APIs on your Google Cloud project from the
    [API Manager page](https://pantheon.corp.google.com/apis/dashboard):

    -   Cloud Storage
    -   Big Query

-   Access your project's Big Query via
    https://bigquery.cloud.google.com/queries/[YOUR-PROJECT-ID] and
    [create a new Data set](https://cloud.google.com/bigquery/docs/datasets).

-   Create an empty sheet in your Google Drive and keep track of it's URL for
    later configuration

-   Create a new Google Apps Script in your Google Drive and paste in all the
    source code files in this repository into their respective files in the
    script.

-   Set the Apps Script to the correct Google Cloud project via *Resources ->
    Cloud Platform project*. Make sure to set it to the new Google Cloud project
    you just created

-   Enable the BigQuery API (v3) via *Resources -> Advanced Google Services*.

-   In order to configure your Apps Script, navigate to *File -> Project
    properties* and select the *Script properties* tab. In this section, you can
    set script wide key value pairs that can be used as a configuration for the
    script. Fill into the table the following properties:

    -   *cloudStorageDest* - The final GCS destination where the generate feed
        CSV resides. Format example:
        gs://[YOUR-BUCKET-NAME]/[FEED-FILE-NAME].csv
    -   *bqDatasetID* - Name of the BQ data set created in the previous steps
    -   *cloudProjectID* - ID of the cloud project created in the previous steps
    -   *routesSheet* - URL of the data source sheet for routes
    -   *cityTranslationsSheet* - URL of the data source sheet for city
    -   *countryTranslationsSheet* - URL of the data source sheet for city
    -   *countryLanguagesSheet* - URL of the data source sheet for country
        languages
    -   *landingPagesSheet* - URL of the data source sheet for landing pages
    -   *classesSheet* - URL of the data source sheet for classes

## Usage instructions

### Initial data setup

-   At this point, the script should be able to run.
-   Before generating any data, the new sheet needs to be initialized with a
    structure and some data. To initialize the sheet with the recommended data
    structure, run the *initSheet* function from within the apps script.
-   This will create the required tabs and columns needed for the script to run
    as intended.
-   Fill in the airline specific data into the generates sheets. For more
    information on the columns, check the DATA.md file for data structure
    definitions to understand what each column is meant for.

### Generating and updating the feed

-   Once the data is filled into the sheet and ready to generate, there are
    mainly 2 Apps Script functions to run after this point:

    1.  *setup():* This function generates all the needed tables in Big Query
        based on the sheet's data structure and sets up the query views. The
        function should be ran only once on a new BQ data set, or in case the
        data structure changes. i.e. Columns changes or query views are updated.

    2.  *run():* This function copies all table data from the sheets into the BQ
        data set (overwrites existing table data in BQ) then triggers a run on
        the top BQ view, which as a result triggers the rest of the views to
        generate the final feed result. Once these queries are executed, the
        result gets delivered as a CSV into Cloud Storage via a BQ export (also
        triggered by this function). For frequest updates, this function can be
        either run manually, from a sheet (via custom menu) or through an [Apps
        Script time
        trigger](https://developers.google.com/apps-script/guides/triggers/installable)

-   After generating the feed based on your data, contact your Google gTech or
    sales specialist to connect the generated CSV file to the requred
    Doubleclick search advertiser account.

-   More details on leveraging inventory feeds in Doubleclick Search can be
    found on the [help
    center](https://support.google.com/ds/answer/7129110?hl=en).

### Disclaimer

This is not an official Google product.