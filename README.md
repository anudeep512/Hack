# Hack
HACKATHON

URL-encoded path of the project: including its namespace
For all the below use cases refer to attached link before writing the spec:

## Project:

Ref: https://docs.gitlab.com/api/projects/#get-a-single-project

A) For getting one single project using URL encoded path of the project: Returns the metadata of the project
curl --header "PRIVATE-TOKEN: <your_token>" \
     "https://gitlab.example.com/api/v4/projects/mygroup%2Fmyproject"


B) All the projects that I have access to: Returns the Metadata of all the projects after filtering [can have multiple params for filtering]
curl --header "PRIVATE-TOKEN: <your_token>" \
     "https://gitlab.example.com/api/v4/projects"

C) Delete a project:
DELETE /projects/:id

D) List of projects of a particular user: Returns list of Metadata of all the projects [can have multiple params for filtering]
GET /users/:user_id/projects

E) Get the users list of a project: Returns List of user objs in JSON
GET /projects/:id/users

F) Get a list of ancestor groups for this project: 
GET /projects/:id/groups

G) Get the list and usage percentage of programming languages used in a project: Returns JSON of (Lang: %)
GET /projects/:id/languages

H) Create a new project:
curl --request POST \
     --header "PRIVATE-TOKEN: <your_token>" \
     --header "Content-Type: application/json" \
     --data '{			—> sent as payload
       "name": "my-new-project",
       "description": "Project created via API",
       "visibility": "private"
     }' \
     "https://gitlab.example.com/api/v4/projects"
 POST /projects

I) Edit Project history:
curl --request PUT --header "PRIVATE-TOKEN: <your-token>" \
     --url "https://gitlab.com/api/v4/projects/<your-project-ID>" \
     --data "shared_runners_enabled=true"
PUT /projects/:id

J) Restore a project that is marked for deletion:
POST /projects/:id/restore

K) Transfer a Project to a new namespace: Returns the metadata JSON of the moved Project
curl --request PUT --header "PRIVATE-TOKEN: <your_access_token>" "https://gitlab.example.com/api/v4/projects/5/transfer?namespace=14"
PUT /projects/:id/transfer

L) Search for the Project if we don’t know the full namespace: Returns the Project Metadata
curl --header "PRIVATE-TOKEN: <your_token>" \
     "https://gitlab.example.com/api/v4/projects?search=my-keyword"

M) Forking a project 
curl --request POST \
     --header "PRIVATE-TOKEN: <your_token>" \
     "https://gitlab.example.com/api/v4/projects/<url_encoded_path>/fork"  


Difference between Repo and Project:
Every GitLab Project contains one repo.
Repo = code storage
Project = repo + full set of GitLab features
GitHub “Repository” = GitLab “Project”

## Repository:

Ref: https://docs.gitlab.com/api/repositories/

A) Get a list of repository files and directories in a project: Returns list of JSON which contain file&folders names
curl --header "PRIVATE-TOKEN: <your_token>" \
     "https://gitlab.example.com/api/v4/projects/<url_encoded_path>/repository/tree"

B) Compare branches/ commits: Returns a JSON payload with commit, commits, diffs
GET /projects/:id/repository/compare
GET /projects/:id/repository/compare?from=main&to=feature/ GET /projects/:id/repository/compare?from=<SHA-1>&to=<SHA-2>
Commit: latest commit for feature/ <SHA-2>
Commits: has all the commits that are not in main/ <SHA-1>
Diffs: combined diff across all extra commits, i.e. the net file changes between main and feature/ <SHA-1> and <SHA-2>

## Branches:

Ref: https://docs.gitlab.com/api/branches/

A) Get the list of all branches in the repo: Returns list of JSON of the metadata of the branch
GET /projects/:id/repository/branches
curl --header "PRIVATE-TOKEN: <your_access_token>" \
  --url "https://gitlab.example.com/api/v4/projects/<url_encoded_path>/repository/branches"

B) Get a single project repository branch: Returns the JSON Metadata of that branch
GET /projects/:id/repository/branches/:branch
curl --header "PRIVATE-TOKEN: <your_access_token>" \
  --url "https://gitlab.example.com/api/v4/projects/5/repository/branches/<url_encoding>“

c) Create repository branch: Returns Metadata JSON of the branch created
POST /projects/:id/repository/branches
curl --request POST \
  --header "PRIVATE-TOKEN: <your_access_token>" \
  --url "https://gitlab.example.com/api/v4/projects/5/repository/branches?branch=newbranch&ref=main"

d) Delete Repo branch: 
DELETE /projects/:id/repository/branches/:branch
curl --request DELETE \
  --header "PRIVATE-TOKEN: <your_access_token>" \
  --url "https://gitlab.example.com/api/v4/projects/5/repository/branches/newbranch"

## Commits:

Ref: https://docs.gitlab.com/api/commits/#create-a-commit-with-multiple-files-and-actions

A) Get a single commit: Returns Metadata of the commit
GET /projects/:id/repository/commits/:sha

B) cherry-pick a commit: Return depends on success/ failure of the cherry-pick
POST /projects/:id/repository/commits/:sha/cherry_pick
curl --request POST \
  --header "PRIVATE-TOKEN: <your_access_token>" \
  --form "branch=main" \
  --url "https://gitlab.example.com/api/v4/projects/5/repository/commits/main/cherry_pick"

C) Revert a commit: Return depends on Pass/ Fail
POST /projects/:id/repository/commits/:sha/revert
curl --request POST \
  --header "PRIVATE-TOKEN: <your_access_token>" \
  --form "branch=main" \
  --url "https://gitlab.example.com/api/v4/projects/5/repository/commits/a738f717824ff53aebad8b090c1b79a14f2bd9e8/revert"

D) For getting diff of a particular commit: 
GET /projects/:id/repository/commits/:sha/diff
curl --header "PRIVATE-TOKEN: <your_access_token>" \
  --url "https://gitlab.example.com/api/v4/projects/<url_token>/repository/commits/main/diff"
If :sha is branch name returns latest commit changes on that branch

E) Getting comments of a commit:
GET /projects/:id/repository/commits/:sha/comments
curl --header "PRIVATE-TOKEN: <your_access_token>" \
  --url "https://gitlab.example.com/api/v4/projects/5/repository/commits/main/comments"

F) For creating a commit by sending JSON payload: 
POST /projects/:id/repository/commits
PAYLOAD=$(cat << 'JSON'
{
  "branch": "main",
  "commit_message": "some commit message",
  "actions": [
    {
      "action": "create",
      "file_path": "foo/bar",
      "content": "some content"
    },
    {
      "action": "delete",
      "file_path": "foo/bar2"
    },
    {
      "action": "move",
      "file_path": "foo/bar3",
      "previous_path": "foo/bar4",
      "content": "some content"
    },
    {
      "action": "update",
      "file_path": "foo/bar5",
      "content": "new content"
    },
    {
      "action": "chmod",
      "file_path": "foo/bar5",
      "execute_filemode": true
    }
  ]
}
JSON
)
curl --request POST \
  --header "PRIVATE-TOKEN: <your_access_token>" \
  --header "Content-Type: application/json" \
  --data "$PAYLOAD" \
  --url "https://gitlab.example.com/api/v4/projects/1/repository/commits"

Manipulating Repository files:

Ref for Repository files: https://docs.gitlab.com/api/repository_files/

b) Get a file from repository: Returns the Data of the file in base64 encoding
GET /projects/:id/repository/files/:file_path
curl --header "PRIVATE-TOKEN: <your_access_token>" \
  --url "https://gitlab.example.com/api/v4/projects/13083/repository/files/app%2Fmodels%2Fkey%2Erb?ref=main"

C) For just getting Metadata from the file: Returns only the metadata of the file
HEAD /projects/:id/repository/files/:file_path
curl --head --header "PRIVATE-TOKEN: <your_access_token>" \
  --url "https://gitlab.example.com/api/v4/projects/13083/repository/files/app%2Fmodels%2Fkey%2Erb?ref=main"

d) Git blame on a file: Returns blame information. Each blame range contains lines and their corresponding commit information.
GET /projects/:id/repository/files/:file_path/blame
curl --header "PRIVATE-TOKEN: <your_access_token>" \
  --url "https://gitlab.example.com/api/v4/projects/13083/repository/files/path%2Fto%2Ffile.rb/blame?ref=main"

E) Git Blame on file with range: Returns list of commits for between specified line numbers
curl --head --header "PRIVATE-TOKEN: <your_access_token>" \
  --url "https://gitlab.example.com/api/v4/projects/13083/repository/files/path%2Fto%2Ffile.rb/blame?ref=main&range[start]=1&range[end]=2"

F) Creating a new file in the Repo: 
POST /projects/:id/repository/files/:file_path
curl --request POST \
  --header 'PRIVATE-TOKEN: <your_access_token>' \
  --header "Content-Type: application/json" \
  --data '{"branch": "main", "author_email": "author@example.com", "author_name": "Firstname Lastname",
            "content": "some content", "commit_message": "create a new file"}' \
  --url "https://gitlab.example.com/api/v4/projects/13083/repository/files/app%2Fproject%2Erb"

G) Update existing file in repository: Returns some data abt the file
PUT /projects/:id/repository/files/:file_path
curl --request PUT \
  --header 'PRIVATE-TOKEN: <your_access_token>' \
  --header "Content-Type: application/json" \
  --data '{"branch": "main", "author_email": "author@example.com", "author_name": "Firstname Lastname",
       "content": "some content", "commit_message": "update file"}' \
  --url "https://gitlab.example.com/api/v4/projects/13083/repository/files/app%2Fproject%2Erb"

H) Delete existing file in repo: 
DELETE /projects/:id/repository/files/:file_path
curl --request DELETE \
  --header 'PRIVATE-TOKEN: <your_access_token>' \
  --header "Content-Type: application/json" \
  --data '{"branch": "main", "author_email": "author@example.com", "author_name": "Firstname Lastname",
       "commit_message": "delete file"}' \
  --url "https://gitlab.example.com/api/v4/projects/13083/repository/files/app%2Fproject%2Erb"

** F,G,H are only for single files
** For performing F, G,H on multiple files we just the Commits API
curl --request POST \
  --header "PRIVATE-TOKEN: <your_access_token>" \
  --header "Content-Type: application/json" \
  --data '{
    "branch": "main",
    "commit_message": "batch update multiple files",
    "actions": [
      {
        "action": "create",
        "file_path": "app/new_file.rb",
        "content": "new file content"
      },
      {
        "action": "update",
        "file_path": "app/project.rb",
        "content": "updated content"
      },
      {
        "action": "delete",
        "file_path": "app/old_file.rb"
      }
    ]
  }' \
  "https://gitlab.example.com/api/v4/projects/13083/repository/commits"



