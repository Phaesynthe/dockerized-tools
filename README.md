# Dockerized Tools

[![License: WTFPL](https://img.shields.io/badge/License-WTFPL-brightgreen.svg)](http://www.wtfpl.net/about/)

My personal preference when developing is to have as few globally installed tools, libraries, or packages as reasonable.
This repository is a collection of tools that I use that expect to be globally installed, but have been mounted inside
a docker container instead. Each tool can be run by invoking the corresponding `docker run` command after the image has
been built. 

**Note**: As of December 2022, this repository is being assembled. Prior to this, my dockerization of tools has been
project related and focused on either one-time solutions to initial problems, or otherwise restricted to the exact
nature of the project in question. This repo represents my intention to stop doing one-off solutions or otherwise
recreating the wheel.

## Tools and Instructions

### Create React App
From this project root, run the following
```bash
cd create-react-app
docker build ./ -t create-react-app 
```

Then navigate to the folder in which you wish to create a new project and run
```bash
docker run -v ${PWD}:/output -e APP_NAME=boilerplate create-react-app
```
**APP_NAME**: Sets the name of the React app that is created

