# Viren Execution API

The Viren Execution API can be used to execute a block of a Viren model or obtain information about a Viren model.
The examples bellow describe the most common use cases of the Viren Execution API.


A nuget package for .NET development is available on https://github.com/tealpartners/viren.net

The swagger for the Viren Rest API can be found on https://execution.viren.be/swagger/ui/index.


## Examples

## Authorization
All communication with the Viren Execution API requires a HTTP Authorization header that contains a valid API Key.

```
Authorization: ApiKey <client_secert>
```

## Execute Calculation

Request id should be a new UUID for every request. Client session id should be a new UUID for every "logical" calculation.

```
POST https://execution.viren.be/v1/calculation
{
    "requestId": "1aba7953-54f3-455a-ab2f-fc0c461fc8cf",
    "project": "Project",
    "model": "Model",
    "version": 1,
    "revision": 0,
    "entryPoint": "RootBlock",
    "root": {
        "Input1": 5
    },
    "globals": {},
    "resultType": 15001,
    "clientSessionId": "47690274-133f-4e75-b0f4-1d3c3f049367"
}


Response
{
    "isValid": true,
    "result": {
        "Output1" : 20
    },    
    "validationMessages": []
}
```

Always check the isValid boolean of the response. If isValid is false the result property should be ignored.


## Execute Batch Calculations

You can send multiple executions in batch to Viren.

```
POST https://execution.viren.be/v2/calculations
{
  "batchCalculationInputItems": [
    {
      "project": "Project",
      "model": "Model",
      "version": 1,
      "revision": 0,
      "entryPoint": "RootBlcok",
      "resultType": 15001,
      "calculationInputs": [
        {
          "requestId": "c4f15f19-76a3-4316-9d73-9a9a51560213",
          "root": {
              "Input1": 3
          },
          "globals": {}
        },
        {
          "requestId": "3ef51147-0a1f-4c70-adab-be943ab81c11",
          "root": {
              "Input1": 10
          }
        }
      ]
    }
  ],
  "clientSessionId": "7b490d44-28cc-42c8-9e76-422f75195098"
}

Response
{
    "validationMessages": [],
    "elapsedMilliseconds": 127,
    "batchCalculationOutputItems": [
        {
            "requestId": "c4f15f19-76a3-4316-9d73-9a9a51560213",
            "result": {
                "Output1": 6.0
            },
            "validationMessages": [],
            "isValid": true
        },
        {
            "requestId": "3ef51147-0a1f-4c70-adab-be943ab81c11",
            "result": {
                "Output1": 20.0
            },
            "validationMessages": [],
            "isValid": true
        }
    ]
}
```


## Optimize

You can optimize by providing the expected value for one output and one or more inpunts to optimize to.


```
POST https://execution.viren.be/v1/calculation/optimize
{
    "requestId": "0dd356dc-4062-40e2-82d3-e4b6c0a521db",
    "project": "Project",
    "model": "Model",
    "version": 1,
    "entryPoint": "RootBlcok",
    "root": {
        "In1": 30
    },
    "globals": {},
    "optimizeInputs": [
        {
            "rootParameterName": "In2",
            "numericOptimizationOptions": {
                "minimum": "0",
                "maximum": "1000"
            }
        }
    ],
    "optimizeOutput": {
        "allowedMargin": 0.1,
        "outputHintValue": "150",
        "resultName": "Out1"
    }
}

Response
{
    "result": {
        "Out1": 149.9951171875,
        "Out2": 130.0
    },
    "input": {
        "In1": 30,
        "In2": 119.9951171875
    },
    "abortReason": "",
    "isValid": true,
    "validationMessages": [  ]
}
```
