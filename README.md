# Create status checks in GitHub

This is an example how to update statuses for GitHub pull requests using ASP.NET Core and Google Cloud.

If you have any issues or questions - feel free to [create an issue](https://github.com/xferra/netcore-jshint/issues).

## Requirements
- [VSCode](https://code.visualstudio.com) (Optional)
- [NET Core](https://www.microsoft.com/net/core) - tested on MacOS (1.0.0-preview2-003131) 
- [NodeJS](https://nodejs.org/en/download/), npm, [jshint](https://www.npmjs.com/package/jshint)
- [GIT](https://git-scm.com)
- [Docker](https://www.docker.com/)
- [Google Cloud SDK](https://cloud.google.com/sdk/)

## Before you start
- Set path to git 'GitPath' in [tasks.json](https://github.com/xferra/netcore-jshint/blob/master/.vscode/launch.json#L30) and/or in Env variables
- [Generate Personal Access Token](https://github.com/settings/tokens) in GitHub. Set 'GitHubToken' in [tasks.json](https://github.com/xferra/netcore-jshint/blob/master/.vscode/launch.json#L32) and [dockerfile](https://github.com/xferra/netcore-jshint/blob/master/Dockerfile#L29)
- Feel free to update 'GitHubName' and 'GitHubUrl' (optional)
- Windows Users: you will need to [execute cmd instead of bash](https://github.com/xferra/netcore-jshint/blob/master/src/Utils/Helper.cs#L61) - TODO

## Getting started (locally)
1. Clone repository
2. `cd` to the `project root/src`
3. Restore dependencies `dotnet restore`
4. Build project `dotnet build`
5. Run `dotnet run` - app started ([http://localhost:5000](http://localhost:5000))

## Getting started (docker)
1. `cd` to the `project root`
2. Build image `docker build -t mycontainer:v1 .`
3. Run `docker run -i -p 8181:8181 -t mycontainer:v1` - app started ([http://localhost:8181](http://localhost:8181))

## Getting started (cloud)
0. You need to [create project](https://cloud.google.com/resource-manager/docs/creating-managing-projects), Enable Billing (free trial available), [Set Default Credentials](https://developers.google.com/identity/protocols/application-default-credentials)
1. Let's say project id 'myproject', docker image name 'gcr.io/myproject/mycontainer:v1', cluster name 'mycluster'. Google App credentials path is correct `GOOGLE_APPLICATION_CREDENTIALS=PATH_TO_FILE`. 
2. Install kubernetes `gcloud components install kubectl`
3. Build image for cloud `docker build -t gcr.io/myproject/mycontainer:v1 .`
4. Push it to Google Container Registry `gcloud docker push gcr.io/myproject/mycontainer:v1`
5. Set the Project `gcloud config set project myproject`
6. Create a cluster `gcloud container clusters create mycluster --num-nodes 1 --machine-type n1-standard-1 --zone europe-west1-b`
7. Configure command line access to the cluster `gcloud container clusters get-credentials mycluster --zone europe-west1-b`
8. Create deployment from image `kubectl run github --image=gcr.io/myproject/mycontainer:v1 --port=8181`
9. Make sure the deployment and pod are running `kubectl get deployments` + `kubectl get pods`
10. Expose deployment to the outside world `kubectl expose deployment github --type="LoadBalancer"`
11. Get external IP address `kubectl get services` - app started (use your external IP)

## GitHub Integration
1. [Create new GitHub integration](https://github.com/settings/integrations)
2. Use any name, url: 'http://external_ip_from_cloud:8181/Home/Webhook', enable 'Commit statuses' (Permissions & events)
3. Create a repo for tests
4. Add integration for test repository: repo_url/settings/installations
5. Enable protected branches and status checks from your integration: repo_url/settings/branches
6. Create PR in test repository, that's it! [Examples](https://github.com/xferra/xferra-Test-PR/pulls).

## Links
1. .NET Core: https://www.microsoft.com/net/core
2. ASP.NET Core: https://docs.asp.net/en/latest/intro.html
3. Docker: https://www.docker.com/
4. Google Cloud: https://cloud.google.com/
5. Kubernetes: http://kubernetes.io/
6. .NET on Google Cloud Platform: https://cloud.google.com/dotnet/docs/
7. GitHub Integrations: https://github.com/integrations
8. GitHub Developer: https://developer.github.com