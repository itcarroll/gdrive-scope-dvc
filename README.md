## Setup

1. setup "gdrive-dvc-tests" in Google Cloud console
  1. create project
  1. enable GDrive API
  1. add OAuth consent screen with "gdrive.file" scope (even necessary?)
  1. create and download OAuth credentials as "credentials.json"
1. setup "gdrive-scope-dvc" project
  1. init git and dvc
  1. create data.csv and data.csv.dvc
1. setup a Google Drive folder
  1. run gdrive.py
  1. note printed Goolge Drive folder Id
1. setup dvc remote
  1. add remote with previously noted folder Id and downloaded gdrive_client_id
     and gdrive_client_secret
  1. run `dvc push` to generate a url, but modify the scope query from "drive"
     to "drive.file"
  1. profit!

## Test Collaboration

```
https://accounts.google.com/o/oauth2/auth? \
  client_id=869640904795-ndtkii9qjrn90qnjp8lj9327a262us9f.apps.googleusercontent.com& \
  redirect_uri=urn:ietf:wg:oauth:2.0:oob& \
  scope=https://www.googleapis.com/auth/drive.file+ \
        https://www.googleapis.com/auth/drive.appdata& \
  access_type=offline& \
  response_type=code& \
  approval_prompt=force
```
