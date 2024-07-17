# akeyless-grpc-php

PHP library for interacting with the Akeyless API using gRPC

## Table of Contents

- [Introduction](#introduction)
- [Installation](#installation)
- [Example](#example)
- [License](#license)

## Introduction

The `akeyless/grpc` library provides a convenient way to interact with the Akeyless API using gRPC.

## Installation

To use `akeyless/grpc` in your project, run the following command `composer require  akeyless/grpc`

```json
    "require": {
        "akeyless/grpc": "*"
    },
```

## Example

```php
<?php

require "vendor/autoload.php";

use Akeyless\gRPC\AkeylessV2ServiceClient;
use Akeyless\gRPC\AuthRequest;
use Akeyless\gRPC\Auth;
use Akeyless\gRPC\GetSecretValueRequest;
use Akeyless\gRPC\GetSecretValue;

// Replace with your actual hostname, options, and channel if needed
$hostname = "127.0.0.1:8085";

// Specify your channel options as needed
$opts = [
    "credentials" => Grpc\ChannelCredentials::createInsecure(),
];

// Create an client of AkeylessV2ServiceClient
$client = new AkeylessV2ServiceClient($hostname, $opts);

// Authenticate
$auth = new Auth([
    "access_id" => "************",
    "access_key" => "**********",
    "access_type" => "access_key",
]);

$authRequest = new AuthRequest();
$authRequest->setBody($auth);

// Auth
list($authResponse, $status) = $client->Auth($authRequest)->wait();

$token = $authResponse->getToken();

$getSecretValue = new GetSecretValue();
$getSecretValue->setToken($token);
$getSecretValue->setNames(["/MyFirstSecret"]);

$getSecretValueRequest = new GetSecretValueRequest();
$getSecretValueRequest->setBody($getSecretValue);

list($getSecretValueResponse, $status) = $client
    ->GetSecretValue($getSecretValueRequest)
    ->wait();

$fields = $getSecretValueResponse->getData()->getFields();

// Iterate over each field
foreach ($fields as $key => $value) {
    $stringValue = $value->getStringValue();  

    // Use the values as needed
    echo "Field: $key, Value: $stringValue\n";
}

```

### License

This project is licensed under the Apache License 2.0. See the LICENSE file for details.
