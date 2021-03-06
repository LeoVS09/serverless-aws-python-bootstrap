# Serverless ASW Python Bootstrap

Base setup of environment and tools to start develop on lambda functions from zero (minimal) configuration

Contains:

* python enviroment setup
* Enviroment variables - setup for work with enviroment variables, you can setup their local and in produciton
* Encripted file variables - setup for store variables in encripted file
* Tests - you can develop you function by run test of functions locally
* S3 bucket usage - Example of usage and testing S3 bucket inside function

## Servless from scratch tutorial

This tutorial use `make` util (it available for linux and windows) to create one file which commands you can use,
but if you prefer type commands by self or not want to use `make`
you can read all command in `Makefile`. Format of file trying to be self decriptive and easy to understand for new commers.

### Quick Start

Just go step by step and you will deploy you function to AWS

```bash
git clone https://github.com/LeoVS09/serverless-aws-python-bootstrap.git
cd ./serverless-aws-python-bootstrap

# Genereta enviroment template
make setup
# Write you secrets to '.env.local' file
# and load enviroment variables
set -o allexport; source ./.env.local; set +o allexport

# Write you project name, app, org into serverless.yml
# app and org you can find into you dashboard.serverless.com

# Then deploy you function to AWS, run on local machine console, not in docker
serverless deploy
```

### Setup local enviroment

This command will generate configuration enviroment `.env.local` file which will be used by docker and serveless

```bash
make setup
```

### In docker isolated linux development

This bootstrap allow (but not require) develop all code inside docker container

This will achive you prevent problems:

* If you develop on windows, but want use linux enviromnt in production, you can develop in linux container
* If you not want install all required tool and packages in global enviroment you machine you can use predefinet container
* If multiple new team members will work on you package you not need to explain what need to install on their machines

Start docker container, sync all local files, get console inside

```bash
make console
```

### Deploy you serverless functions

Commands to deploy you function

>Note login to serverless when deploy first time
Deploy to aws cloud by serverless

```bash
serverless deploy
```

Login to serverless

```bash
serverless login
```

### Dependencies

For manage dependencies used few tools: `pipenv` and `serverless-python-requirements`

* `pipenv` - allow install dependencies locally and in docker, manage virtual enviroment and lock file for control dependencies. It uses Pipfile and Pipfile.lock. For install some package justt use `pipenv install <package>`
* `serverless-python-requirements` - install and package dependencies for your lambda function before deploy. It configurated in `serverless.yaml`, and read `Pipfile.lock` for package dependencies before deploy.

### Create new function

You can create new function by `sls create` command

This command will generate for you new handler file, add new function to `serverless.yml` config and add intial test

You can use predefined `make` command for it

```bash
sls create function -f newFunction --handler api/functionc/index 
```

### Create S3 Bucket

**Serverless will create S3 bucket by self**, by specified resouce configuration, but you also can use

```bash
aws s3api create-bucket --bucket python-botstrap-example-bucket --region eu-central-1 --create-bucket-configuration LocationConstraint=eu-central-1
```

### Serverless tips

You can deploy faster by update only codee and dependencies of individual function
Example for `hello` function

```bash
sls deploy function -f hello
```

Get logs of deployed `hello` function

```bash
serverless logs -t -f hello
```

Invoke function in cloud and print their log

```bash
serverless invoke --log --function=hello
```

Invoke function locally and print logs

```bash
serverless invoke local --log --function=hello
```

### Enviroment variables

You can add `.env` file and all variables will be loaded into your lambdas, also you can reference them in config.

```yml
# serverless.yaml
  SECRET_FUNCTION_TOKEN: ${env:SECRET_FUNCTION_TOKEN}
```

## Tests

For run unit test powered by `pytest` and `moto`, simply run

```bash
make test
```

### Auto-scaling

Different providers have different limitations.

#### AWS Lambda

By default, AWS Lambda limits the total concurrent executions across all functions within a given region to 100. The default limit is a safety limit that protects you from costs due to potential runaway or recursive functions during initial development and testing. To increase this limit above the default, follow the steps in [To request a limit increase for concurrent executions](http://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html#increase-concurrent-executions-limit).

## Not in Docker

For run dpleoy not in docker run this

```bash
export AWS_ACCESS_KEY_ID=<your-key-here>
export AWS_SECRET_ACCESS_KEY=<your-secret-key-here>
# AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY are now available for serverless to use
serverless deploy

# 'export' command is valid only for unix shells. In Windows - use 'set' instead of 'export'
```
