# Cloudified Go Instructions

## Overview
This course is an enrichment of the existing Go Bookshelf App Google Cloud example project located [here](https://cloud.google.com/go/getting-started/tutorial-app)

In following the course exercises bring you a basic level of familiarity with the following Google Cloud products:
- App Engine : a deployment platform 
- Cloud Datastore : a NoSQL datastore for general purpose data storage
- Cloud Storage : a Blob store for binary data like images
- Google OAuth 2 : an authentication mechanism
- Cloud Pub/Sub : a publish and subscribe event database
- Stackdriver Logging : application monitoring tool
- Extra Credit: Google Kubernetes Engine : a deployment platform

## Setup
### Local Development Environment Setup
#### Go Setup
- Install Go for your platform. See [getting started](https://golang.org/doc/install)
  - OS X Homebrew command: `brew install go` or see [this](https://ahmadawais.com/install-go-lang-on-macos-with-homebrew/)
#### Editor Setup
- I used VSCode. Use whatever you like
#### Google Cloud Account & SDK Setup
- Create or link a Google account to Google Cloud Console
- Create a Project in Google Cloud
  - Can do with **gcloud** once installed
    - `gcloud projects create [project ID]` - takes about 30 seconds
  - Then link a billing account to your project. This is required since you will be using money ($3-$8 total if that).
- Install the Cloud SDK for your platform. See [quickstarts](https://cloud.google.com/sdk/docs/quickstarts)
  - OS X Homebrew command: `brew cask install google-cloud-sdk` or see [this](https://cloud.google.com/sdk/docs/quickstart-macos)
- Install the *gcloud* command line tool
- Initialize your gcloud config:
  - `gcloud init`
- Do the [Before you begin](https://cloud.google.com/go/getting-started/tutorial-app#before-you-begin) steps
  - See next section for SDK setup
  - Log in with `gcloud auth application-default login`
  - Verify config with `gcloud config list`
  - Set your project ID with `gcloud config set project [YOUR_PROJECT_ID]`
- Use [APIs & Services](https://console.cloud.google.com/apis/dashboard) to enable the following APIs in the project: Cloud Datastore, Cloud Pub/Sub, Cloud Storage JSON, Stackdriver Logging, and Google+ APIs. Some may already be enabled. In that case then you don't have to do anything for that particular API.
  - Cloud Datastore
  - Cloud Pub/Sub
  - Cloud Storage JSON
  - Stackdriver Logging
  - Stackdriver Error Reporting
  - Stackdriver Trace
  - Stackdriver Debugger
  - Stackdriver Profiler
  - Stackdriver Monitoring
  - Google+ API


### Go Bookshelf App setup 
#### Tutorial Steps 
- [Before you Begin](https://cloud.google.com/go/getting-started/tutorial-app#before-you-begin)

#### Download App code split:22m
Since this tutorial was initially created with an older version of Golang you need to prepend **GO111MODULE=off** 
to your download command in order to force the Go downloader to put the code in **$GOPATH/src**:
- `$ GO111MODULE=off go get -u -d github.com/GoogleCloudPlatform/golang-samples/getting-started/bookshelf/app`

### Google Cloud Datastore Setup
#### Tutorial Steps
- [Using Structured Data in Go](https://cloud.google.com/go/getting-started/using-structured-data)
  - You can click through with **USE CLOUD DATASTORE>**. The other options require more work than we have time for here. Feel free to explore.
- [Using Cloud Datastore with Go](https://cloud.google.com/go/getting-started/using-cloud-datastore)
  
#### Configure and deploy your app
- Uncomment line 86 in **config.go** and set the project name to be your project name.
    - Example: 
      - `DB, err = configureDatastoreDB("techkno19")`
- Navigate to [GCP Datastore](https://console.cloud.google.com/datastore). This should take you to a Welcome page.
  - Select **SELECT DATASTORE MODE**
  - On the next page select a region. I picked **nam5 (United States)**.
  - Click **Create Database**
- Run app locally and deploy your app to the cloud
  - Local deploy (should be instant): 
    - `$ cd app`
    - `$ go run app.go auth.go template.go`
     Try adding a few books to the app locally
  - Google App engine deploy (5 minutes or so):
    - `$ cd app` if not already in that directory
    - `$ gcloud app deploy`
    - Try viewing or adding a few books to the deployed app. The books you added locally should be present in the deployed app view.
- **Error case**: Try saving an image. This will trigger an error like so:
  - `could not parse book from form: could not upload file: storage bucket is missing - check config.go`
  -  We will come back and trigger this error later


### Google Cloud Storage split:43m
#### Tutorial Steps
- [Using Cloud Storage with Go](https://cloud.google.com/go/getting-started/using-cloud-storage)
#### Storage Bucket Setup
- Set up bucket with
  - `$ gsutil mb gs://techkno19-library`
- Set default ACL for public visibility
  - `$ gsutil defacl set public-read gs://techkno19-library`
- Uncomment lines 97 and 98 in **config.go**
  - Example:
    - `StorageBucketName = "techkno19-library"`
    - `StorageBucket, err = configureStorage(StorageBucketName)`
	- Run the app locally and deploy again to google cloud
- Edit an existing book and save an image. This time the image saving should succeed.
- Delete the old version [here](https://console.cloud.google.com/appengine/versions?_ga=2.89776860.-610306329.1543525261)

### User Authentication split:57m
#### Tutorial Steps
- [Authenticating Users with Go](https://cloud.google.com/go/getting-started/authenticate-users)
#### Online OAuth Setup
- Go to the [credentials setup]([https://console.cloud.google.com/apis/credentials?_ga=2.235112390.-610306329.1543525261)
- Go to OAuth consent screen
  - Example set config props:
    - Name=Go Bookshelf App
    - Authorized Domains = techkno19.appspot.com
    - Make sure you hit save before it auto-redirects you.
- Back on the credentials screen make OAuth client ID
  - Example config props:
    - Name=Go Bookshelf Client
    - Authorized redirect URIS
      - http://localhost:8080/oauth2callback
      - http://techkno19.appspot.com/oauth2callback
        - If you get an error here saying it must be listed as a valid domain you have to go back to the OAuth consent screen and add your app domain.appspot.com to the list of authorized domains.
      - https://techkno19.appspot.com/oauth2callback
- Copy Client ID and secret
  - Client ID=951915662698-6fuf1ngjchgu78nsrj538cuhi73aobg2.apps.googleusercontent.com
  - Client Secret=T7Q9hab4s7Iq33LCVfqtHypb
#### Code OAuth Setup
- Edit line 111 of **config.go** like so:
  - ```
    OAuthConfig = configureOAuthClient("951915662698-6fuf1ngjchgu78nsrj538cuhi73aobg2.apps.googleusercontent.com", "T7Q9hab4s7Iq33LCVfqtHypb")
    ```
- Edit **app/app.yaml** and set the OAUTH2_CALLBACK environment variable to have your project ID like so:
  - `OAUTH2_CALLBACK: https://techkno19.appspot.com/oauth2callback`
- Run and deploy the app locally and in the cloud
- Delete the old version [here](https://console.cloud.google.com/appengine/versions?_ga=2.89776860.-610306329.1543525261)
- **Error case**: go to the My Books page. There should be an error stating an index is missing like so:
- 
    ```
    could not list books: datastoredb: could not list books: rpc error: code = FailedPrecondition desc = no matching index found. recommended index is:
    - kind: Book
      properties:
      - name: CreatedByID
      - name: Title
    ```

  Come back to this later for error handling demonstration.
- Add indexes - Do this before running the app again
  - `$ gcloud datastore indexes create index.yaml`
  - This takes about 2 minutes.

## Modification split:94m-8.5
### Logging
#### Tutorial Steps
- [Logging App Events](https://cloud.google.com/go/getting-started/logging-application-events)

#### Add Logging to the app
- Review the logging code in **app.go** near line 319
  - `type appHandler func`
  - `type appError struct`
  - `func (fn appHandler) ServeHTTP`
    - shows a way to use a custom error handling struct
- Add log statements to **app.go**
  - In **func createHandler**
    - `log.Printf("New book %v added by %v", book.Title, book.CreatedBy)`
  - In **func updateHandler**
    - `log.Printf("Updated book %v", book.Title)`
  - In **func deleteHandler**
    - `log.Printf("Deleted book %v", id)`
  - In **func loginHandler**
    - `log.Println("Login successful")`
  - In **func logoutHandler**
    - `log.Println("Logout successful")`
- Run the app locally and deploy again to the cloud
- Do a few things that trigger logs like 
  - adding a few books
  - updating a book 
  - logging in and out
- Review the application logs for the actions you just performed. This may take about 30 seconds to show up.
- If you don't see the log messages make sure to save your modifications, deploy, and then try again.
- Go to Stackdriver Logging and select the appropriate app version (has 100% next to it)
  
### Cloud Pub/Sub split 108m-8.5
#### Tutorial Steps
- [Using Cloud Pub/Sub with Go](https://cloud.google.com/go/getting-started/using-pub-sub)
#### Adding pub/sub to the app
- Uncomment `Pubsubclient` in **config.go**
- Run app locally
- Run **pubsub_worker**
  - `$ cd pubsub_worker`
  - `$ PORT=8081 go run worker.go`
- Add a few well known books
  - A Tale of Two Cities by Charles Dickens
  - Crime and Punishment by Fyodor Dostoevsky
  - Pride and Prejudice by Jane Austen
- Review the results come in as updated pictures for books
- Deploy pubsub worker to App Environment
  - `$ cd pubsub_worker`
  - `$ gcloud app deploy`
  - `$ cd ../app`
  - `$ gcloud app deploy`
- Deploy your app again. Check to see that the app pictures show up on your deployed app.
  - https://techkno19.appspot.com
- Check the pubsub worker like so:
  - https://worker-dot-techkno19.appspot.com


### End 2h9m

## Next Steps
### GKE Deployment