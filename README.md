# Documentation Identity Analytics

## Introduction

This repository holds the documentation for the installation, the configuration and the run of Identity Analytics when installed as self-managed.  
The documentation is used as an input to build the documentation [https://developer.radiantlogic.com/](https://developer.radiantlogic.com/)

## Repository Structure

The repository is structured such as each branch corresponds to the documentation of a specific version.  

## Gatsby .env

To use the repository to build locally in Gatsby use the following code in the `GATSBY_DEPLOY_REPOS` parameter of the `.env` file (some adaptation may be necessary):

```sh
GATSBY_DEPLOY_REPOS='
[
,
  {
    "name": "ia",
    "displayName": "Identity Analytics",
    "description": "This guide provides a high-level overview of Identity Analytics. This documentation includes the user guides, integration guides for Identity Analytics along with the different modules included.",
        "links": [
      {
        "text": "SEE SELF-MANAGED GUIDES",
        "href": "/ia/version-1.2/#2"
      }
    ],
    "remote": "https://github.com/radiantlogic-v8/documentation-ia-selfmanaged.git",
    "patterns": [
      "home-pages/**",
      "documentation/**"
    ],
    "deployBranches": [
      {
        "name": "version-1.2",
        "displayName": "v1.2"
      }
    ]
  }
]
'
'
```
