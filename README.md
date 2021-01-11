# ecs-bg-deployment-example

## ECS Blue/Green deployment using CloudFormation

### Set up

Create a new AWS CodeCommit repository and copy all files from this repository to the new one.

Set parameters in the following file.

- `cloudformation/ecs-parameter.yml`
- `cloudformation/pipeline-parameter.yml`
- `cloudformation/stack-config.json`

Create a ecs sample app stack.

```
$ aws cloudformation create-stack \
  --template-body file://cloudformation/ecs.yml \
  --cli-input-yaml file://cloudformation/ecs-parameter.yml
```

Create a pipeline stack.

```
$ aws cloudformation create-stack \
  --template-body file://cloudformation/pipeline.yml \
  --cli-input-yaml file://cloudformation/pipeline-parameter.yml
```

## ECS Blue/Green deployment using CodeDeploy

### Set up

Create a new AWS CodeCommit repository and copy all files from this repository to the new one.

Set parameters in the following file.

- `codedeploy/ecs-parameter.yml`
- `codedeploy/pipeline-parameter.yml`
- `codedeploy/taskdef.json`
- `codedeploy/deployment-group.json`
  - `loadBalancerInfo.prodTrafficRoute.listenerArns` will be set later.

Register the ECS task defintion.

```
$ aws ecs register-task-definition \
  --cli-input-json file://codedeploy/taskdef.json \
  --region ap-northeast-1
```

Create a ecs sample app stack.

```
$ aws cloudformation create-stack \
  --template-body file://./codedeploy/ecs.yml \
  --cli-input-yaml file://./codedeploy/ecs-parameter.yml
```

Rewrite taskdef.json as follows.

- `"nginxdemos/hello:latest"` -> `"<IMAGE1_NAME>"`

Set the parameters `loadBalancerInfo.prodTrafficRoute.listenerArns` in `codedeploy/deployment-group.json`.

Create the AWS CodeDeploy resources.

```
$ aws deploy create-application \
  --application-name tutorial-bluegreen-app \
  --compute-platform ECS \
  --region ap-northeast-1
$ aws deploy create-deployment-group \
  --cli-input-json file://codedeploy/deployment-group.json \
  --region ap-northeast-1
```

Create a pipeline stack.

```
$ aws cloudformation create-stack \
  --template-body file://cloudformation/pipeline.yml \
  --cli-input-yaml file://cloudformation/pipeline-parameter.yml
```
