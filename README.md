Cerbos PHP SDK
===============

PHP client library for the [Cerbos](https://github.com/cerbos/cerbos) open source access control solution. This library
includes RPC clients for accessing the Cerbos PDP.

Find out more about Cerbos at https://cerbos.dev and read the documentation at https://docs.cerbos.dev.

Installation
-------------

Examples
--------

### Creating a client


```php
$clientBuilder = new CerbosClientBuilder("http://localhost:3592", new HttplugClient(), null, null, null);
$client = $clientBuilder->build();
```

### Check a single principal and resource

```php
$principal = Principal::newInstance("john")
                ->withRole("employee")
                ->withPolicyVersion("20210210")
                ->withAttribute("department", "marketing")
                ->withAttribute("geography", "GB");
                
$resourceAction = ResourceAction::newInstance("leave_request", "xx125")
                    ->withActions(["view:public", "approve"])
                    ->withPolicyVersion("20210210")
                    ->withAttribute("department", "marketing")
                    ->withAttribute("geography", "GB")
                    ->withAttribute("owner", "john");
                    
$checkResourcesResult = $this->client->checkResources($principal, array($resourceAction), null);

$resultEntry = $checkResourcesResult->find("xx125");

if ($resultEntry->isAllowed("view:public")) { // returns true if `view:public` action is allowed
    // ...
}

if ($resultEntry->isAllowed("approve")) { // returns true if `approve` action is allowed
    // ...
}
```

### Check a single principal and multiple resource & action pairs

```php
$principal = Principal::newInstance("john")
                ->withRole("employee")
                ->withPolicyVersion("20210210")
                ->withAttribute("department", "marketing")
                ->withAttribute("geography", "GB");
                
$resourceAction = ResourceAction::newInstance("leave_request", "xx125")
                    ->withAction("approve")
                    ->withPolicyVersion("20210210")
                    ->withAttribute("department", "marketing")
                    ->withAttribute("geography", "GB")
                    ->withAttribute("owner", "john");
                    
$resourceAction1 = ResourceAction::newInstance("leave_request", "xx225")
                    ->withAction("defer")
                    ->withPolicyVersion("20210210")
                    ->withAttribute("department", "marketing")
                    ->withAttribute("owner", "john");
                    
$checkResourcesResult = $this->client->checkResources($principal, array($resourceAction, $resourceAction1), null);

$resultEntry = $checkResourcesResult->find("xx125");

if ($resultEntry->isAllowed("approve")) { // returns true if `approve` action is allowed
    // ...
}

$resultEntry = $checkResourcesResult->find("xx225");

if ($resultEntry->isAllowed("defer")) { // returns true if `defer` action is allowed
    // ...
}
```