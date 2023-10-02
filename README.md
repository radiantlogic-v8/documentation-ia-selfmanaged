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
  {
    "name": "ia",
    "displayName": "Identity Analytics",
    "description": "This guide provides a high-level overview Identity Analytics. This documentation includes the user guides and integration guides for Identity Analytics along with the different modules included.",
        "links": [
      {
        "text": "Identity Analytics",
        "href": "/ia/iap_2.0/#0"
      }
    ],
    "remote": "https://github.com/radiantlogic-v8/documentation-identity-analytics.git",
    "patterns": [
      "home-pages/**",
      "documentation/**"
    ],
    "deployBranches": [
      {
        "name": "iap_2.0",
        "displayName": "v2.0"
      }
    ]
  }
]
'
'
```
