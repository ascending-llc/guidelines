# Ascending-ADM Project Backend Checklist:

This guide is for backend devops engineer who is working on ADM projects with existing templates.   

## Frontend Related
  1. Create a CloudFormation stack with all the roles and security groups
  1. Make sure the frontend-related S3 Bucket is public   
  1. Check front codepipeline building script (node version, build:${env}, ....)
  1. Cloudfront error behaviour (403, 404) and https setting

## Backend Codepipelie

  1. Check Git Repo and Owner in backend pipeline
  1. Check the ServiceRole for AWS::CodeBuild::Project, make sure you have all the permissions for resources
  1. Make sure build-layer.sh and deploy.sh have been uploaded into Git repo (unless you change the build script.)
  1. Check the variables that will be passed into build-layer.sh and deploy.sh


## Backend Resources (template.yaml)

### Lambda

  1. Check CodeUri, Handler and Role in template.yaml
  1. Include 'Access-Control-Allow-Headers:': 'origin, x-requested-with' in 'headers' in response/return to front-end
  1. Check general configuration (Memory, Timeout, SecurityGroup)
  1. If you do hotfix on AWS console, make sure the deployed code is consistent with local env.

### API Gateway

  1. Test api functionality inside API Gateway is not recommended. Please use Postman
  1. Authorizer needs to be created under AWS::Serverless::Api, but please also add them in each Lambda function that needs authorization (Under Events).
  1. The return content of Lambda Authorizer should be policy document
  1. You can modify API URL in API Gateway console.
  1. Check Gateway Responses in API Gateway console, especially when you have authorizer (set up 401 response or front end will frequently get CORS error).

### DynamoDB

  1. Only define attributes that are related to keys or indexes of a table under AWS::DynamoDB::Table -> AttributeDefinitions
  1. json_util from dynamodb_json is useful for converting dynamoDB item to json object.
  1. dynamoDB supports Map and List


