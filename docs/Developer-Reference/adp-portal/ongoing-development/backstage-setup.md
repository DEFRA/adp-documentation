---
title: Backstage Setup
summary: Backstage setup for the ADP Portal
authors:
    - James Coxhead
date: 2024-01-22
weight: -11
---
# Backstage Setup

The ADP Portal is built on [Backstage](https://backstage.io/), an open-source platform for building developer portals. Backstage is a Node application which contains a backend API and React based front end. This page outlines the steps you need to follow to set up and run Backstage locally.

[[_TOC_]]

# 🏗️ Setup

## Install pre-requisites

Backstage has a few requirements to be able to run. These are detailed in the [Backstage documentation](https://backstage.io/docs/getting-started/#prerequisites), with some requirements detailed below.

### 🐧 WSL

Backstage requires a UNIX environment. If you're using Linux or a Mac you can skip this section, but if you're on a Windows machine you will need to install [WSL](https://learn.microsoft.com/en-us/windows/wsl/about).

WSL can either be installed via the command line (follow [Microsoft&#39;s instructions](https://learn.microsoft.com/en-us/windows/wsl/install)) or from the [Microsoft Store](https://apps.microsoft.com/detail/windows-subsystem-for-linux/9P9TQF7MRM4R?hl=en-US&gl=US). You will then need to install a Linux distribution. Ubuntu is recommended; either download from the [Microsoft Store](https://apps.microsoft.com/detail/9PN20MSR04DW?hl=en-US&gl=US) or run `wsl --install -d Ubuntu-22.04` in your terminal.

Familiarise yourself with:

* [Best practices for setting up your development environment](https://learn.microsoft.com/en-us/windows/wsl/setup/environment)
* [WSL and VS Code](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode)
* [WSL and Git](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-git)
* [Linux for Beginners](https://ubuntu.com/tutorials/command-line-for-beginners#1-overview) if you're unfamiliar with Linux and BASH.

> ⚠️ Everything you do with Backstage from this point forwards must be done in your WSL environment. **Don't attempt to run Backstage from your Windows environment - it won't work!**

### Node.js

You will need either Node 16 or 18 to run Backstage. It will not run on Node 20.

The recommended way to use the correct Node version is to use [nvm](https://github.com/nvm-sh/nvm).

> ⚠️ If on a PC make sure you install and configure nvm in your WSL environment.

You will then need to install Yarn globally. Run `npm install --global yarn` in your WSL environment.

### Git

Make sure you've got Git configured. If on WSL [follow the steps](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-git) to make sure you've got Git configured correctly - your settings from Windows will not carry over.

Ensure you have a GPG key set up so that you can sign your commits. See the guide on [verifying Git signatures](/DEFRA-FFC-Project-Wiki-Homepage/Defra-Azure-Platform/Development/Verify-GitHub-commit-signatures). If you have already set up a GPG key on Windows this will need to be exported and then imported in to your WSL environment.

To export on Windows using Kleopatra, [see here](https://www.gpg4win.org/doc/en/gpg4win-compendium_25.html). To import using gpg on WSL, [see here](https://unix.stackexchange.com/questions/184947/how-to-import-secret-gpg-key-copied-from-one-machine-to-another).

### 🔨 Build tools

If installing WSL for the first time you will likely need to install the build-essential package. Run `sudo apt install build-essential`.

### ☁️ Azure CLI

Check if you have the Azure CLI installed in your WSL environment. Run `az --version`. If this returns an error you need to install the Azure CLI: `curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash`. See [Install the Azure CLI on Linux](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt#option-1-install-with-one-command).

We have integrated Backstage with Azure AD for authentication. For this to work you will need to sign in to the O365_DEFRADEV tenant via the Azure CLI.

## Project Setup

After installing and configuring pre-requisites we can clone the adp-portal repo, configure Backstage, and run the application.

> ⚠️ Remember, if on Windows these steps must be followed in your WSL environment.

### 📃 Clone the repo

If you haven't already, create a folder in your Home directory where you will can clone your repos.

```bash
~$ mkdir projects && cd projects
```

Clone the [adp-portal](https://github.com/defra-adp-sandpit/adp-portal) repo into your projects folder.

### Set environment variables

Client IDs, secrets, etc for integrations with 3rd parties are read from environment variables. In the root of a repo there is a file named env.example.sh. Duplicate this file and rename it to env.sh.

A senior developer will be able to provide you with the values for this file.

A private key is also required for the GitHub app. Again, a senior developer will be able to provide you with this key.

> ℹ️ Later on down the line we are hoping to move these environment variables to Key Vault

To load the environment variables in to your terminal session run `. ./env.sh`. Make sure you include both periods - the first ensures that the environment variables are loaded into the correct context.

### ▶️ Run the application

The application needs to be run from the /app folder - run `cd app` if you're in the root of the project.

Run the following two commands to install dependencies, and build and run the application:

```shell
yarn install
yarn dev
```

To stop the application, press `<kbd>`Ctrl`</kbd>`+`<kbd>`C`</kbd>` twice.

# 🆘 Troubleshooting

If you have issues starting Backstage, check the output in your terminal. Common errors are below:

"*Backend failed to start up Error: Invalid Azure integration config for dev.azure.com: credential at position 1 is not a valid credential*" - Have you loaded your environment variables? Run `. ./env.sh` from the root of the repo, then try running the application again.

*"MicrosoftGraphOrgEntityProvider:default refresh failed, AggregateAuthenticationError: ChainedTokenCredential authentication failed"* - have you logged in to the Azure CLI? Run `az login` and make sure you sign in to the O365_DEFRADEV tenant. Try running the application again.
