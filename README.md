# Jenkins Pipeline

- used for build and test java code using build tools like maven and code quality control tools like sonarqube and artifacts repo like nexus and event notification service like slack

## Installing the Services

- Jenkins:https://www.jenkins.io/doc/book/installing/linux/
- Nexus：https://help.sonatype.com/repomanager3/installation-and-upgrades/run-as-a-service
- Sonar Qube: https://medium.com/@deshdeepakdhobi/how-to-install-and-configure-sonarqube-on-aws-ec2-ubuntu-22-04-c89a3f1c2447

## Tools

- use package manager to download JDK and find in `/usr/lib/jvm`
- install Maven directly with automatic installation
- install git if there is not git install on the virtual machine/server
- install sonar qube with automatic installation v4.7
- install nexus with automatic installation with the latest version 3.9.6 and create a hosted repo name vprofile-repo

## Credentials

- sonar secret token as secret text in jenkins credential (created in security section of my account)
- nexus login info as username/password in jenkins credential (created by following the workflow after inital login)
- slack toekn as secret text in jenkins credential (created when adding jenkins to channel in workspace)

## Plugin

- Nexus Artifact Uploader
- SonarQube Scanner
- Build Timestamp
- Pipeline Maven Integration
- Pipeline Utility Steps
- Slack Notification
