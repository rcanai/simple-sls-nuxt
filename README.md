# simple-ssr-nuxt

NuxtJS using a simple constitution  Server side render

## Constitution

### nodebrew

```bash
$ brew install nodebrew;
$ mkdir -p ~/.nodebrew/src;
$ nodebrew install-binary v12.13.1;
$ nodebrew use v12.13.1;
$ echo 'export PATH=$HOME/.nodebrew/current/bin:$PATH' >> ~/.bash_profile;
$ source ~/.bash_profile;
$ brew install yarn --ignore-dependencies;
```

### node-gyp Bug :)

```bash
$ sudo rm -rf /Library/Developer/CommandLineTools;
$ xcode-select --install;
```

### Server Nodejs Module

✗ Express + aws-serverless-express 
○ Fastify + aws-serverless-express 

## Development

```bash
$ yarn install;
$ yarn run dev;
```

## Local generate

```bash
$ export IS_GENERATE=true;
$ yarn run generate;
```
___

## Create

[create-nuxt-app](https://github.com/nuxt/create-nuxt-app)

```bash
$ yarn create nuxt-app simple-ssr-nuxt;
✨  Generating Nuxt.js project in simple-ssr-nuxt
? Project name simple-ssr-nuxt
? Project description My first-rate Nuxt.js project
? Author name rcanai
? Choose the package manager Yarn
? Choose UI framework None
? Choose custom server framework None (Recommended)
? Choose Nuxt.js modules Axios, Progressive Web App (PWA) Support
? Choose linting tools ESLint
? Choose test framework None
? Choose rendering mode Universal (SSR)
? Choose development tools jsconfig.json (Recommended for VS Code)
```

## [sls] Deploy

```bash
# 1. First (Create Stack) - Codepipeline ~ Serverless
$ aws cloudformation deploy \
  --template-file aws/cfn-deploy.yml \
  --stack-name staging-sls-nuxt-deploy \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides \
  Env=staging \
  GitHubToken=XXX \
  ApiKey=XXX \
  Domain=XXX \
  --profile XXX;

# 2. First (Create Stack) - Cloudfront ~ Rout53
$ aws cloudformation deploy \
  --template-file aws/cfn-front.yml \
  --stack-name staging-sls-nuxt-front \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides \
  Env=staging \
  SSLArn=XXX \
  BasicAuthLambdaArn=XXX \
  Domain=XXX \
  --profile XXX;
```

**The first deployment takes about 40 minutes :(**

```bash
# 1. Second and more (Update Stack) - Codepipeline ~ Serverless
$ aws cloudformation deploy \
  --capabilities CAPABILITY_NAMED_IAM \
  --template-file aws/cfn-deploy.yml \
  --stack-name staging-sls-nuxt-deploy \
  --profile XXX;

# 2. Second and more (Update Stack) - Cloudfront ~ Rout53
$ aws cloudformation deploy \
  --capabilities CAPABILITY_NAMED_IAM \
  --template-file aws/cfn-front.yml \
  --stack-name staging-sls-nuxt-front \
  --profile XXX;
```

```bash
# Remove Basic Auth
$ aws cloudformation deploy \
  --template-file aws/cfn-front.yml \
  --stack-name staging-sls-nuxt-front \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides \ aws cloudformation delete-stack --stack-name slsnuxt-staging --profile XXX;
  BasicAuthLambdaArn="" \
  --profile XXX;
```

## [sls] Delete

```bash
$ aws s3 rm s3://staging-sls-nuxt-deploy-deploy --recursive --profile XXX;
$ aws cloudformation delete-stack --stack-name staging-sls-nuxt-deploy --profile XXX;
$ aws cloudformation delete-stack --stack-name staging-sls-nuxt-front --profile XXX;
$ aws s3 rm s3://staging-simple-sls-nuxt-static-bucket --recursive --profile XXX;
$ aws cloudformation delete-stack --stack-name slsnuxt-staging --profile XXX; # serverlessの物が残ってしまう
```
