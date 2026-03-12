> **DEPRECATED**: This example is no longer maintained and is kept only for reference purposes.
 
 # Meteor.js - Clever Cloud Node.js ESM demo

This repository contains the Metor.js demo application source code (generated via `meteor create`), with scripts needed to deploy on Clever Cloud using the forked Node.js ESM (v14.21.4). It's based on [official documentation](https://guide.meteor.com/using-node-v14.21.4#installing-node-in-linux). 

You'll need a [Clever Cloud account](https://console.clever-cloud.com/) and [Clever Tools](https://github.com/CleverCloud/clever-tools).

## Setup Clever Tools

```bash
npm i -g clever-tools
clever login
```

## Init the project

```bash
git clone https://github.com/CleverCloud/meteorjs-node-esm
cd meteorjs-node-esm
```

## Create and configure the application on Clever Cloud

`enable_esm.sh` downloads and setup the Node.js ESM build from Meteor.js. It's launched by `check_version.sh` within a dedicated build instance. This script also prints Node.js and NPM versions before launching the app: 

```bash
clever create --type meteor
clever scale --build-flavor M # Meteor.js needs a bigger instance to build
clever env set CC_PRE_BUILD_HOOK "./enable_esm.sh"
clever env set CC_PRE_RUN_HOOK "./check_version.sh"
```

Set the application domain as `ROOT_URL` environment variable:

```bash
clever env set ROOT_URL "https://$(clever domain | tr -d ' ')"
```

Create a MongoDB add-on and set its connection URI as `MONGO_URL` environment variable:

```bash
MONGO_ID=$(clever addon create mongodb-addon meteor_esm -F json | jq -r .id)
MONGO_URL=$(clever addon env -F json ${MONGO_ID} | jq -r .MONGODB_ADDON_URI)
clever env set MONGO_URL ${MONGO_URL}
```

## Deploy on Clever Cloud

```bash
git add . && git commit -m "Init application"
clever deploy
clever open
```
