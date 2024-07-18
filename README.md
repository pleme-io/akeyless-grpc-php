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
use Akeyless\gRPC\Auth;
use Akeyless\gRPC\AuthRequest;
use Akeyless\gRPC\GetSecretValue;
use Akeyless\gRPC\GetSecretValueRequest;
use Akeyless\gRPC\ListItems;
use Akeyless\gRPC\ListItemsRequest;

// Replace with your actual hostname, options, and channel if needed
$hostname = "localhost:8085";

// Specify your channel options as needed
$opts = [
    "credentials" => Grpc\ChannelCredentials::createInsecure(),
];

// Create an client of AkeylessV2ServiceClient
$client = new AkeylessV2ServiceClient($hostname, $opts);

// Authenticate
$auth = new Auth([
    "access_id" => "********",
    "access_key" => "********",
    "access_type" => "access_key",
]);

$authRequest = new AuthRequest();
$authRequest->setBody($auth);

// Auth
list($authResponse, $status) = $client->Auth($authRequest)->wait();

$token = $authResponse->getToken();

$listItems = new ListItems();
$listItems->setToken($token);

$listItemsRequest = new ListItemsRequest();
$listItemsRequest->setBody($listItems);

//List items
list($listItemsResponse, $status) = $client
    ->ListItems($listItemsRequest)
    ->wait();

foreach ($listItemsResponse->getItems() as $item) {
    echo $item->getItemName() . PHP_EOL;
}

$getSecretValue = new GetSecretValue();
$getSecretValue->setToken($token);
$getSecretValue->setNames(["/MyFirstSecret"]);

$getSecretValueRequest = new GetSecretValueRequest();
$getSecretValueRequest->setBody($getSecretValue);

//Get secret value
list($getSecretValueResponse, $status) = $client
    ->GetSecretValue($getSecretValueRequest)
    ->wait();

$fields = $getSecretValueResponse->getData()->getFields();

// Iterate over each field
foreach ($fields as $key => $value) {
    $stringValue = $value->getStringValue();

    // Use the values as needed
    echo "Field: $key, Value: $stringValue" . PHP_EOL;
}

```

### License

This project is licensed under the Apache License 2.0. See the LICENSE file for details.
