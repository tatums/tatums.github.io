---
layout: post
title: "Cloudformation ApiGateway and Lambda"
date: 2016-08-25 07:44:12 -0500
comments: true
categories: AWS cloudformation apigateway lambda
---

Recently, I've been excited by [serverless](http://martinfowler.com/articles/serverless.html)
technology. I began using the [serverless framework](http://serverless.com/)
for code boilerplate and deployment. After some time using the framework, I began feeling pain.
Serverless is an excellent project, but it's moving **very** fast. For example, The
framework uses cloudformation for resource dependencies such as dynamoDb, ApiGateway, roles and permissions (to name a few).
Cloudformation is **also** moving very fast. Support for [ApiGateway was added to cloudformation on April
18th, 2016](https://aws.amazon.com/about-aws/whats-new/2016/04/aws-cloudformation-adds-support-for-amazon-api-gateway-and-updated-resource-support/).
 As new features are added to cloudformation, you'll be stuck waiting for
serverless to implement features for feature parity. I've started using
cloudformation direclty and relying on bash scripts for deployment. I'm quite
happy with the results.

### Cloudformation stack

Once we have a
[cloudformation template](https://github.com/tatums/cloudformation-apigateway-lambda/blob/master/cloudformation.json),
the AWS cli provides us with everthing we need. Using the AWS CLI we can create the stack like so.

```bash
$ aws cloudformation create-stack \
	--stack-name hello-world \
	--template-body file://./cloudformation.json \
	--capabilities CAPABILITY_IAM && \
	aws cloudformation wait stack-create-complete \
		--stack-name hello-world
```

The first command fires off an async create request to AWS.
The second command tells our shell to wait for stack creation to complete.

After that's complete, we'll have created a few resources in AWS. =)  Next, we'll need a way to deploy.


### Deployment
We have a few tasks for a complete deployment. We should seperate out the
lambda depoyment and the ApiGateway deployment, but in this case I did not.

* Update Lambda Code - Install any dependencies, zip our code, and upload it.
* Publish a Version - From the latest version, It will tag a copy.
* Update the alias - Our lambda is pointed to an alias. This will point the lambda to our new version.
* deploy ApiGateway - Any changes we make to ApiGateway requires a deploy.


The script takes two args. The `api-gateway-id` and the `function-name`.

```bash
$ ./deploy.sh abc123 hello-word
```

```bash
#!/bin/bash

apiId=$1
functionName=$2
profile=personal

YELLOW='\033[0;33m'
WHITE='\033[0m' # No Color

function zipLambda {
  say "Zipping files." && \
  rm -rf target && \
  mkdir -p target && \
  cp -r *.js package.json target/ && \
  pushd target && \
  npm install --production && \
  zip -r "${functionName}.zip" . && \
  popd
}

function say {
  printf "\n${YELLOW} $@ ${WHITE}\n"
}

function updateLambdaCode {
  say "Uploading new lambda code." && \
  aws lambda update-function-code --function-name $functionName --zip-file "fileb://target/${functionName}.zip" --profile $profile
}

function publishVersion {
  say "Publishing a new version." && \
  aws lambda publish-version --function-name $functionName --profile $profile
}

function updateAlias {
  version=$(aws lambda list-versions-by-function --function-name $functionName --profile personal | grep Version | tail -n 1 | cut -d '"' -f 4) && \
  say "Updating the alias to version ${version}." && \
  aws lambda update-alias --function-name $functionName --function-version $version --name prod --profile $profile
}
function deployApiGatway {
  say "Deploying to Api Gateway." && \
  aws apigateway create-deployment --rest-api-id $apiId --stage-name v1 --profile $profile
}
printf "\n🚀🚀🚀 SHIP IT!!! 🚀🚀🚀 \n\n"
zipLambda && \
  updateLambdaCode && \
  publishVersion && \
  updateAlias && \
  deployApiGatway
```



