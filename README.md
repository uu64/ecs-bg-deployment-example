# ecs-bg-deployment-example

## Set up

Set parameters in the following file.

- `cloudformation/ecs-parameter.yml`
- `cloudformation/pipeline-parameter.yml`
- `cloudformation/stack-config.yml`

Create a ecs sample app stack.

```
aws cloudformation create-stack \
  --template-body file://./cloudformation/ecs.yml \
  --cli-input-yaml file://./cloudformation/ecs-parameter.yml
```

Create a pipeline stack.

```
aws cloudformation create-stack \
  --template-body file://./cloudformation/pipeline.yml \
  --cli-input-yaml file://./cloudformation/pipeline-parameter.yml
```