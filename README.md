# docker-amazonlinux2-codedeploy
Custom Docker Image to Test and Debug AWS CodeDeploy locally with Node.js.

## Motivation

AWS CodeDeploy is a powerful service for automating deployments to Amazon EC2, AWS Lambda, and on-premises servers. However, it can take some effort to get complex deployments up and running or to identify the error in your application when something goes wrong.

The proper way to deal with CodeDeploy is by testing and debugging it locally and once everything working properly fire your deployment process in AWS CodeDeploy.

However, to have a CodeDeploy environment up and running locally is not so trivial, at least until now! :beers:

## About this image

Amazonlinux in Docker (created via `FROM amazonlinux:2`) is so bare and empty that it doesn't even have basic stuffs like sudo or passwd. New AWS EC2 instances do. Plus this image contains the current `Node.js LTS version` **(12)** and `Yarn` Package Manager.

Now, we can work very close to a fresh EC2 instance running in Docker.

On top of it, we have all `AWS CodeDeploy` stuff installed to you playing with it locally.

## How to use this image

### Preparing your container

- Run a container in **privileged mode** with `docker run --name awscodedeploy -d --privileged richardsilveira/amazonlinux2-codedeploy:latest`.
- Go inside it with `docker exec -it awscodedeploy bash`
- Start the CodeDeploy agent `service codedeploy-agent start`
- Check the status of the agent through `service codedeploy-agent status` _(sometimes you'll need to exec `start` command above again)_
- Create an **codedeploy-local alias** with `alias codedeploy-local='sudo /opt/codedeploy-agent/bin/codedeploy-local'`

> Note: Don't leave your container, you'll work a lot here while testing your deployment.

### Testing/Debugging your deployment

Go to your **app root directory** _(at same level of your `appspec.yml` file)_, zip your app and copy the zip into the running container with `docker cp <yourapp>.zip awscodedeploy:/`.

Now, **back to your running container** run `codedeploy-local --bundle-location <yourapp>.zip -t zip --deployment-group my-deployment-group`

As soon the execution is done, a message like this `See the deployment log at /opt/codedeploy-agent/deployment-root/my-deployment-group/<randomidentifier>-local/logs/scripts.log for more details` will be showed at console, then you check whats is going on with your deployment by running `cat /opt/codedeploy-agent/deployment-root/my-deployment-group/<randomidentifier>-local/logs/scripts.log`.

> Note: Don't forget that **it's a regular EC2 instance** running locally, so, you can install everything you need to reproduce your application up and running in AWS Cloud environment.
