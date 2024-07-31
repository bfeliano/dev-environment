# Development Environment Repo

<a id="readme-top"></a>
<br />
<div align="center">
  <a href="https://github.com/bfeliano/dev-environment">
    <img src="config/logo.png" width="80" height="80" alt="Repo Logo">
  </a>
  <p align="center">
    This repository contains instructions for setting up a Windows OS for
    development purposes.
  </p>
</div>

## About The Project

I created this repository to provide a centralized guide for setting up a
Windows OS for development purposes.  
These instructions reflect my personal configuration choices and preferences.
Please note that I am not responsible for any issues that may arise from
downloading and installing the recommended software. You can follow these
instructions at your own risk. Make sure you review and understand each step
before proceeding.  

## Getting Started

Here you can find the software, tools and configurations I use, with a focus on
WSL (Windows Subsystem for Linux).  

### Windows Configuration

#### Install WSL

The easiest way to install WSL is by running the command below in
Command Prompt:  

```sh
wsl --install -d Ubuntu-24.04
```

You can also search for different Linux distributions available for WSL using
the command below:  

```sh
wsl --list --online
```

#### Softwares to Download

Use Ctrl + Click to open the websites in new tabs.  

* [Brave Browser](https://brave.com/download/)
* [Visual Studio Code](https://code.visualstudio.com/download)

##### VS Code Setup

###### Theme

I like to use the `Monokai Pro` theme in VS Code. You can download it from
Visual Studio Extensions or directly from the
[Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=monokai.theme-monokai-pro-vscode).
To install and apply it, navigate to **Settings > Themes > Color Theme**.  
Additionally, I recommend setting the **File Icon Theme** to `Seti` for a
better visual experience.  

###### Extensions

[WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl):
This is required to be able to launch WSL in VS Code.  
[GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens):
Enhances your Git experience.  
[Hashicorp Terraform](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform):
Provides syntax highlighting and autocompletion for Terraform.  
[GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot):
Assists in writing code faster and smarter.  

###### Settings

`Format on Save` set to **true**, to automatically format code every time you
save a file. However, ignoring markdown files by adding the following to
`settings.json`.  

``` json
"editor.formatOnSave": true,
"[markdown]": {
        "editor.formatOnSave": false
    },
```

`Explorer: Auto Reveal` set to **false**, to prevent directories from
auto-expanding when opening files.  

### WSL Configuration

#### Packages Installation

**Note:** The commands in this section should be executed from a WSL terminal.  

* **NodeJS:** `sudo apt install nodejs`  
Node.js  executes JavaScript code outside a web browser. Node.js lets
developers use JavaScript to write cli tools and for server-side scripting.  

* **Live Server** `sudo npm install live-server -g`  
This is a little development server with live reload capability. Having the
page reload automatically after changes to files can accelerate development.  
After installed you can run using the command `live-server`

### WORK IN PROGRESS!! - This is not yet the final version of this document
