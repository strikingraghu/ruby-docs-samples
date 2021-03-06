# Ruby Google Cloud Tasks sample for Google App Engine

This sample application shows how to use [Google Cloud Tasks](https://cloud.google.com/cloud-tasks/)
on Google App Engine [flexible environment][appengine-flex].

App Engine queues push tasks to an App Engine HTTP target. This directory
contains both the App Engine app to deploy, as well as the snippets to run
locally to push tasks to it, which could also be called on App Engine.

`create_task.rb` is a simple command-line program to create tasks to be pushed to
the App Engine app.

`app.rb` is the main App Engine app. This app serves as an endpoint to
receive App Engine task attempts.

`app.yaml` configures the app for App Engine Ruby flexible
environment.

* [Setup](#setup)
* [Running locally](#running-locally)
* [Deploying to App Engine](#deploying-to-app-engine)
* [Running the tests](#running-the-tests)

## Setup

Before you can run or deploy the sample, you need to do the following:

1.  Refer to the [appengine/README.md](readme) file for instructions on
    running and deploying.
1.  Enable the Cloud Tasks API in the [Google Cloud Console](https://console.cloud.google.com/apis/api/tasks.googleapis.com).
1.  Set up [Google Application Credentials](https://cloud.google.com/docs/authentication/getting-started).
1.  Install dependencies:
    ```
    bundle install
    ```

## Creating a queue

To create a queue using the Cloud SDK, use the following gcloud command:

    gcloud beta tasks queues create-app-engine-queue my-appengine-queue

Note: A newly created queue will route to the default App Engine service and
version unless configured to do otherwise.

## Deploying the app to App Engine flexible environment

Deploy the App Engine app with gcloud:

    gcloud app deploy app.yaml

Verify the index page is serving:

    gcloud app browse

## Run the Sample Using the Command Line

Set environment variables:

First, your project ID:

```
export GOOGLE_CLOUD_PROJECT=my-project-id
```

Then the queue ID, as specified at queue creation time. Queue IDs already
created can be listed with `gcloud beta tasks queues list`.

```
export QUEUE_ID=my-appengine-queue
```

And finally the location ID, which can be discovered with
`gcloud beta tasks queues describe $QUEUE_ID`, with the location embedded in
the "name" value (for instance, if the name is
"projects/my-project/locations/us-central1/queues/my-appengine-queue", then the
location is "us-central1").

```
export LOCATION_ID=us-central1
```
### Using App Engine Push Queues
Create a task, targeted at the `/log_payload` endpoint, with a payload specified:

```
ruby create_task.rb $GOOGLE_CLOUD_PROJECT $LOCATION_ID $QUEUE_ID hello
```

The App Engine app serves as a target for the push requests. It has an
endpoint `/log_payload` that reads the payload (i.e., the request body) of the
HTTP POST request and logs it. The log output can be viewed with:

    gcloud app logs read

Create a task that will be scheduled for a time in the future using the
a second argument:

```
ruby create_task.rb $GOOGLE_CLOUD_PROJECT $LOCATION_ID $QUEUE_ID hello 30
```

[appengine-flex]: https://cloud.google.com/appengine/docs/flexible/ruby
