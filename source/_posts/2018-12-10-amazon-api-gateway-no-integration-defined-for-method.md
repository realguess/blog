title: Amazon API Gateway No Integration Defined for Method
date: 2018-12-10T12:00:00
categories: [Computing]
tags: [serverless, sls, apigateway, aws]
---

Encountered the following problem when deploying with the [Serverless Framework][] (`v1.27.3`) to [Amazon API Gateway][]:

```
CloudFormation - CREATE_IN_PROGRESS - AWS::ApiGateway::Deployment - ApiGatewayDeployment
CloudFormation - CREATE_FAILED - AWS::ApiGateway::Deployment - ApiGatewayDeployment

An error occurred: ApiGatewayDeployment - No integration defined for method
```

The problem was not the bug from the Serverless side, but was originated with manually created resource without integration defined for the `POST` method:

```
$ aws apigateway get-integration --rest-api-id xxxxxxxxxx --resource-id 0xxxxx --http-method post

An error occurred (NotFoundException) when calling the GetIntegration operation: No integration defined for method
```

After removing the resource. The deployment works again.

In conclusion, if there is a resource and one of its method has no integration, the REST API cannot be deployed. Either removing the resource or creating an integration will resolve the problem.

The following script will look for a REST API for no integration error:

```sh
#!/bin/sh
#
# Search Amazon API Gateway for resources that have no integration defined for
# method.

API_NAME=example.com

REST_API_ID=$(aws apigateway get-rest-apis --query='items[?name==`'$API_NAME'`].id | [0]' --output=text)
RESOURCES=$(aws apigateway get-resources --rest-api-id=$REST_API_ID --query='items[*].id' --output=text)

for resource in $(echo "$RESOURCES")
do
    for method in $(aws apigateway get-resource \
        --query='resourceMethods && keys(resourceMethods)'  \
        --output=text                                       \
        --rest-api-id=$REST_API_ID                          \
        --resource-id=$resource)
    do
        if [ "$method" != "None" ]
        then
            aws apigateway get-integration \
                --rest-api-id=$REST_API_ID  \
                --resource-id=$resource     \
                --http-method=$method > /dev/null
            if [ $? -ne 0 ]
            then
                aws apigateway get-resource \
                    --rest-api-id=$REST_API_ID  \
                    --resource-id=$resource
            fi
        fi
    done
done
```

References:

- https://github.com/serverless/serverless/issues/5100
- https://stackoverflow.com/questions/46087890/no-integration-defined-for-method-choose-a-stage-where-your-api-will-be-deploy


[Amazon API Gateway]: https://aws.amazon.com/api-gateway/
[Serverless Framework]: https://serverless.com
