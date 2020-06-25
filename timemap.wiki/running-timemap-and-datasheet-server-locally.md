### Timemap and Datasheet-server Together

This document also appears as a blog on our OSS page, which you can find [here](https://engineering.forensic-architecture.org/timemap-and-datsheet-server).

This document details how to set up [timemap](https://github.com/forensic-architecture/timemap) and [datasheet server](https://github.com/forensic-architecture/datasheet-server) in development. If you already have a tested instance of timemap/datasheet-server and are ready to deploy it in production, see [our devops repository](https://github.com/forensic-architecture/devops/tree/master/timemap_datasheet).

Timemap and datasheet-server work together to interactively serve geospatial events from a Google Sheet. The sheet must be formatted appropriately for this to work. This tutorial will walk you through setting up for [this example Google Sheet](https://docs.google.com/spreadsheets/d/1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk), which is already appropriately formatted. Once you have this demo working, it is as easy as changing one line in configuration to point the application to a different datasheet.

This tutorial is also useful to learn the way that we use timemap, datasheet server, and Google Sheets at [Forensic Architecture](https://www.forensic-architecture.org/) to create interactive geospatial platforms for open source human rights research. We use this setup to present findings from an invesigation once it is complete; but we also use it dynamically throughout to visualise information across the different stages of research. The density or scarcity of events in the axes of time and space informs how we work, and in which areas of an investigation we apply focus. It is intended as a general tool that can be used in other open source and human rights research.

### Structuring Data in Investigations

Take a moment to look over the structure of the data in [the example sheet](https://docs.google.com/spreadsheets/d/1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk). Information about the structure of individual tabs and cells is included in the sheet as comments. (You can over over the cells with yellow tags in the top right corner to read these.) An important note is that datasheet-server, the tool that presents data from the sheet as JSON, can be used generically to present data from _any Google sheet_ in JSON objects generically. The data in this example sheet demonstrates only how to specifically structure a sheet so that it can serve data (through datasheet-server) specifically for timemap, a frontend application that visualises data from JSON objects that are structured appropriately.

The example sheet contains data across several different tabs, which are separated into two distinct types: **source tabs** and **export tabs**.

* **Source tabs** - Source tabs do not need to be structured directly in timemap format. In our investigations, we find that it is often useful to structure them along the lines of timemap data types, as ultimately parts of their data will be used in timemap, but they often also contain additional data that is relevant to an investigation. They represent the source data for an investigation, which is often more expansive than simply that which is shown in a timemap instance. Source tabs should be designed for human/researcher readability and workability.
* **Export tabs** - As their name suggests, export tabs reference the data in source tabs and filter and format it correctly for itmemap. As a matter of good practice, _we only place referential data in export tabs_. In other words, cells in export tabs consist entirely of the Excel-like expressions that Google Sheets offers to populate a cell with data from elsewhere in the sheet. See the comments in the [EXPORT_EVENTS](https://docs.google.com/spreadsheets/d/1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk/edit#gid=974355827) tabs for more information and example of how referential data works in Google Sheets.

## Digging In

Let's get started.

### Datasheet Server

The first step is to set up a local instance of [datasheet-server](https://github.com/forensic-architecture/datasheet-server) that is connected to the [example sheet](https://docs.google.com/spreadsheets/d/1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk). Clone and install dependencies with the following commands in a bash/zsh shell:

```bash
git clone https://github.com/forensic-architecture/datasheet-server
cd datasheet-server
npm install
```

To connect datasheet-server to the example sheet, you'll need to create an account on [Google Cloud Platform](https://console.cloud.google.com/home/dashboard). In GCP, do the following:
1. [create a new project](https://console.cloud.google.com/projectcreate)
2. In the new project, [enable the Sheets API](https://console.cloud.google.com/apis/library/sheets.googleapis.com).
3. From the project's credentials page, [create a new service account key](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). After you've created the service account, you should download the JSON credentials onto your local machine.

The downloaded 'credentials.json' contains information for your service account such its associated email and its private key. Inside the datasheet-server folder you cloned earlier, copy the [env.exampe](/env.example) file in the top layer to a file named '.env' that is also in the root directory. Open up this file and replace the value in the double quotes in the lines that begin with `SERVICE_ACCOUNT_EMAIL` and `SERVICE_ACCOUNT_PRIVATE_KEY` with the values 'client_email' and 'private_key' (in 'credential.json') respectively.

Inside the 'src' folder, open up the '[sheets_config.js](sheets_config.js)' file in an editor. This file is prepopulated with the appropriate set of blueprinters to serve the data from the example sheet for a timemap instance. Double check that the 'id' value in the object corresponds to the ID of the example sheet in Google. (You can check this by looking at the URL path when the sheet is open in a browser.)

Assuming that you have configured your service account on GCP appropriately, you should now be able to run a development instance of datasheet server with the following command in a bash/zsh shell in the datasheet-server directory:
```bash
npm run dev
```

You should see something similar to the following output:
```
Enabling CORS in development...
Connected to 1UC7DkCFeUXHfpUxUGruExwFbP4pqVBdJLOKfo6wDDGk.
===================
grant access to: {{your service account email}}
===================
Started on port 4040
```

Note that datasheet server doesn't have any UI at its root path (http://localhost:4040) yet; so don't panic when this returns an error! You can confirm that the server is running by visiting [http://localhost:4040/api](https://localhost:4040/api), which should return a JSON object that contains datasheet-server's version number.

Now that the local instance of datasheet-server is running and connected to the example sheet, you can update it with the sheet's data by visiting following URL in a browser: [http://localhost:4040/api/update](http://localhost:4040/api/update).

This should return the following success message:

```json
{"success":"All sheets updated"}
```

Your instance of datasheet-server is now configured and running! Visit [http://localhost:4040/api/blueprints](http://localhost:4040/api/blueprints) to see the endpoints that have now been made available via the example sheet (one for each export tab).

### Timemap

Now that datasheet-server is running with data, we are ready to read from it with a timemap instance. Clone the repo and install dependencies:

```bash
git clone https://github.com/forensic-architecture/timemap
cd timemap
npm install
```

Assuming your instance of datasheet server is still running at [http://localhost:4040](http://localhost:4040/api), the default configuration should be all set to pull data from it. Inside the timemap repository, run the following command:

```bash
npm run dev
```

You should now be able to visit [http://localhost:8080](http://localhost:8080) to see an instance of timemap running that presents the data from the example sheet. 
