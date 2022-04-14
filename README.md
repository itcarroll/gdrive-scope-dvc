# README

[As requested](https://github.com/iterative/dvc/issues/4477#issuecomment-1064425666), I have begun experiments with DVC and the
GDrive API "drive.file" scope. The goal is to resolve whether this scope may replace the excessively permissive "drive" scope for
all of DVC's interactions with GDrive.

The "drive.file" scope allows the application using the API to "View and manage Google Drive files and folders that you have opened or created with this app"[^1]. Suppose a user grants this access to a Google Drive folder they own called "dvcstore", then that user should be able to use `dvc push` and `dvc pull`. Suppose our user shares this Google Drive folder to a collaborator, then that collaborator should be able to `dvc pull` and (if an Editor) `dvc push`.

For round one, we can conduct tests with a [google cloud project](https://dvc.org/doc/user-guide/setup-google-drive-remote#using-a-custom-google-cloud-project-recommended). As DVC adopts the client_id and client_secret of the cloud project, the anyone authorized on the google cloud project with access to the client_id and client_secret can get a token and are (we hope!) effectively the same client as far as GDrive is concerned.

## Setup

The following steps only needed to be done once by the Google Drive folder Owner or Google Drive shared drive Manager.

1. setup "gdrive-dvc-tests" in Google Cloud console
    1. create project
    1. enable GDrive API
    1. add OAuth consent screen with "gdrive.file" scope (even necessary?)
    1. create and download OAuth credentials as "credentials.json"
1. setup "gdrive-scope-dvc" repository
    1. init git and dvc
    1. create data.csv and data.csv.dvc
1. setup a Google Drive folder
    1. run gdrive.py to grant "gdrive-dvc-tests" access to users Google Drive **and
       create a folder that will be used by DVC**.
    3. note printed Goolge Drive folder id
1. setup a DVC remote
    1. add remote with previously noted folder id and downloaded gdrive_client_id
       and gdrive_client_secret
    1. run `dvc push` to generate a url, **but modify the scope query** from "drive"
       to "drive.file"
    1. profit!

## Test Collaboration

The following should be tested before any attempts to make a PR to DVC:

- [x] Owner of "dvcstore" folder can `dvc push`
- [x] Owner of "dvcstore" folder can `dvc pull` from a separate clone of this project
- [ ] Editor of "dvcstore" folder can `dvc pull`
- [ ] Editor of "dvcstore" folder can `dvc push` changes (with a new commit to this project)
- [ ] Manager of "dvcstore" shared drive can `dvc push`
- [ ] Manager of "dvcstore" shared drive can `dvc pull` from a separate clone of this project
- [ ] Content Manager of "dvcstore" shared drive can `dvc pull`
- [ ] Content Manager of "dvcstore" shared drive can `dvc push` changes (with a new commit to this project)

The normal DVC flow for authorizing the remote should work **except for the change to the scope**. When DVC spits out
a url including "scope=https://www.googleapis.com/auth/drive", append ".file". Leave the "appdata" scope, which I presume
DVC needs for some undocumented reason.

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

[^1]: https://developers.google.com/identity/protocols/oauth2/scopes
