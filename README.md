# Unified Push Admin Client

[![Coverage Status](https://coveralls.io/repos/github/bucharest-gold/unifiedpush-admin-client/badge.svg?branch=master)](https://coveralls.io/github/bucharest-gold/unifiedpush-admin-client?branch=master)
[![Build Status](https://travis-ci.org/bucharest-gold/unifiedpush-admin-client.svg?branch=master)](https://travis-ci.org/bucharest-gold/unifiedpush-admin-client) 
[![Known Vulnerabilities](https://snyk.io/test/npm/unifiedpush-admin-client/badge.svg)](https://snyk.io/test/npm/unifiedpush-admin-client) 
[![dependencies Status](https://david-dm.org/bucharest-gold/unifiedpush-admin-client/status.svg)](https://david-dm.org/bucharest-gold/unifiedpush-admin-client)

[![NPM](https://nodei.co/npm/unifiedpush-admin-client.png)](https://npmjs.org/package/unifiedpush-admin-client)

A client for connecting to the AeroGear UnifiedPush servers admin REST API - https://aerogear.org/docs/specs/aerogear-unifiedpush-rest/#home

|                 | Project Info  |
| --------------- | ------------- |
| License:        | Apache-2.0  |
| Build:          | make  |
| Documentation:  | http://bucharest-gold.github.io/unifiedpush-admin-client/  |
| Issue tracker:  | https://github.com/bucharest-gold/unifiedpush-admin-client/issues  |
| Engines:        | Node.js 4.x, 5.x, 6.x

## Installation

`npm install unifiedpush-admin-client -S`

## Usage

    'use strict';

    let adminClient = require('unifiedpush-admin-client');

    let baseUrl = 'http://127.0.0.1:8080/ag-push';

    let settings = {
        username: 'admin',
        password: 'admin'
    };

    adminClient(baseUrl, settings)
      .then((client) => {
        return client.applications.find()
          .then((applications) => {
            console.log('applications', applications);
          });
      })
      .catch((err) => {
        console.log('Error', err);
      });


## Variants

The 0.2.0 release brings in the methods to interact with the Variants.  To learn more about what exactly a variant is in terms of the Unified Push Server, checkout their docs here:  https://aerogear.org/docs/unifiedpush/ups_userguide/index/#_useful_terminology

The `find` method for variants is similiar to the `find` method for applications. The difference is that you need to specify a `type` along with the `pushAppId`

Example of Find all Variants for Type for a Push Application(_note: we are only showing a snippet of code here. assume we have the client object already_)

    const variantOptions = {
      pushAppId: 'PUSH_APPLICATION_ID',
      type: 'android'
    };


    client.variants.find(variantOptions).then((variants) => {
      console.log(variants); // will show the list of android variants
    });


For the available Device Types, there is a constant called `client.DEVICE_TYPES` for convience,  https://github.com/bucharest-gold/unifiedpush-admin-client/blob/master/lib/device-types.js


Create example:

    const variantOptions = {
      pushAppId: 'PUSH_APPLICATION_ID',
      name: 'My Cool App',
      type: 'android',
      android: {
        googleKey: 'SOME_GOOGLE_API_KEY',
        projectNumber: 'SOME_GOOGLE_PROJECT_NUMBER'
      }
    };

    client.variants.create(variantOptions).then((variant) => {
      console.log(variant); // will be the created variant with some extra meta-data added
    });

This above example specifies the `type` as `android`, so we therefore must have an anrdoid object with the specific thing an android variant needs.

If the type was `adm` for Amazom push, then we would need an adm object with those specific options

## API Documentation

http://bucharest-gold.github.io/unifiedpush-admin-client/

### Boostrapping

There is also a convience method under applications called `client.applications.boostrap`.

Use can create a Push Application with Variants in one shot.  Check the documentation for all available options, but here is a quick example:

    const bootstrapper = {
            pushApplicationName: 'Boostrap All',
            androidVariantName: 'Android Name',
            androidGoogleKey: '12345',
            androidProjectNumber: '54321',
            iosVariantName: 'iOS Name',
            iosPassphrase: 'redhat',
            iosCertificate: __dirname + '/../../scripts/test-ios-cert.p12',
            simplePushVariantName: 'SimplePush Name',
            windowsVariantName: 'Windows Name',
            windowsType: 'wns',
            windowsSid: '12345',
            windowsClientSecret: 'secret',
            admVariantName: 'ADM Name',
            admClientId: '12345',
            admClientSecret: 'secret'
        };

        client.applications.bootstrap(bootstrapper).then((app) => {
          console.log(app);
        });

### Notable Changes from 0.2.0 to 0.3.0

* All Variant Types have been implemented
* Integration Tests added

### Server Setup

At the moment, you must enable "Direct Access Grants" on the UnifiedPush Server for this to work.

### Note on Testing

For testing we are using a slightly modified version of the UnifiedPush Server.  It is based off of this PR: https://github.com/aerogear/aerogear-unifiedpush-server/pull/677 and uses scenario 3.  The modified built war is located here:  https://github.com/lholmquist/aerogear-unified-push-server/releases/tag/0.0.1

The reason for doing this allows us to more easily script the setup/testing process.  The only thing that PR really does is decouple keycloak and the UPS,  it does not change how the REST endpoints work, just how to authenticate to them.

Once that PR is merged then we can point to the official release.

Running the `scripts/start-server.sh` and `scripts/start-kc-server.sh` will setup both the unified push server and keycloak server for local testing

## Contributing

Please read the [contributing guide](./CONTRIBUTING.md)