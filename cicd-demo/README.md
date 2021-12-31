# CI/CD for SPA & Microservices

Simplify building on AWS by adopting this [CDK](https://docs.aws.amazon.com/cdk/latest/guide/home.html) boilerplate for an [IaC Pipeline](https://searchitoperations.techtarget.com/tip/Building-an-infrastructure-as-code-pipeline-in-the-cloud) of a CI/CD for SPA & Microservices. If you're new to AWS, this can help. It contains pre-made architectural decisions allowing you to get started quickly. Yet because it's IaC, you also have flexibility. You can easily update your architecture by pushing code modifications to your repository. 

## Context

Philippines has an abundance of developers and a scarcity of devops and cloud engineers. Therefore, a common question we get from early stage startups is: "We developed X. What services do we pick to deploy it in AWS?" In most cases, we can work backwards, provide them recommendations, and then direct them to online resources. But in the case of modern technologies like microservices and data lakes, they often get back to us stumped.

One founder even asked if we have something like Azure DevOps, GCP Cloud Run, or IBM Code Engine to easily deploy their containerized services. After some discussions, we understood the sentiment: Yes, the AWS breadth of services provides maximum flexibility; but startups are willing to trade that for ease and speed of getting started. Concretely, they just want out-of-the-box solutions at the early stages of their products where:

1. most architectural decisions are made for them
2. sensible defaults are provided

## Relevance

This applies to all startups that want to deploy a SPA front-end + microservice APIs back-end. It also highlights the usage of CDK Pipelines and AWS Developer Tools which potentially simplifies the technology aspect of devops for a developer-centric market like the Philippines.

## Prerequisites

* a development machine with [Yarn](https://yarnpkg.com/getting-started/install) and [CDK](https://docs.aws.amazon.com/cdk/latest/guide/getting_started.html)
* [IAM admin](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) with [programmatic access](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)
* if going to use CodeCommit, relevant IAM users should have [credentials set up](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_ssh-keys.html)
* if going to use GitHub, [access token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token) needs to be in [Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/tutorials_basic.html)

## Initial Deployment

* Configure `cdk.context.yaml`. See [Schema](#schema) below.
* Install the dependencies:
```bash
yarn install
npx yaml2json cdk.context.yaml > cdk.context.json
```
* Test that the configuration and code synthesizes properly:
```bash
cdk synth
```
* [Bootstrap CDK](https://docs.aws.amazon.com/cdk/latest/guide/bootstrapping.html) in the AWS account and region to be used:
```bash
cdk bootstrap \
  --cloudformation-execution-policies arn:aws:iam::aws:policy/AdministratorAccess \
  aws://<account>/<region>
```
* Deploy this pipeline:
```bash
cdk deploy
```
* If CodeCommit is created for this IaC code, [connect the local to the remote repo](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-connect.html#how-to-connect-local).

## Screenshots

![IaC Pipeline 1](https://res.cloudinary.com/engr-lynx/image/upload/v1623983025/iac/iac-pipeline-1.png "IaC Pipeline 1")
![IaC Pipeline 2](https://res.cloudinary.com/engr-lynx/image/upload/v1623983025/iac/iac-pipeline-2.png "IaC Pipeline 2")

## Tech Stack

[CDK](https://docs.aws.amazon.com/cdk/latest/guide/home.html), [CDK Pipelines](https://aws.amazon.com/blogs/developer/cdk-pipelines-continuous-delivery-for-aws-cdk-applications/), TypeScript

## FAQ

#### How does an IaC pipeline simplify building on the cloud?

An IaC pipeline allows the tech team to codify their architecture. This has many benefits among them being:
1. Deployment is consistent and repeatable as the code defines what is being deployed as opposed to a manual process.
1. All the benefits granted by a repository to application code now also applies to software architecture.
1. Human error in configuration is minimized. And if ever there is an error, it's just a matter of modifying the code and pushing the changes.

#### Why use CDK instead of CloudFormation scripts?

CloudFormation is very verbose. Just deploying a simple VPC with a few subnets becomes very complicated. Contrast that to using CDK where sensible defaults are provided. Furthermore, the use of a programming language in CDK enables the use of programming constructs. For example, intead of relying on having multiple templates and picking one based on a specific scenariio, an if-else construct can be used.

#### What value does CDK Pipelines add to CDK?

CDK Pipelines enables the creation of a CI/CD pipeline for the architecture itself! This means that all the benefits of a CI/CD pipeline for applications now also apply to architecture.

## Schema

#### archi

| Field | Type | Description |
| :- | :- | :- |
| `id` | `string` | **Required**. ID for this pipeline |
| `pipeline` | **_`pipeline`_** | **Required**. pipeline definition |

#### pipeline

| Field | Type | Description |
| :- | :- | :- |
| `repo` | **_`repo`_** | **Required**. source stage definition |
| `build` | **_`build`_** | build stage definition |
| `validate` | **_`validate`_** | validate stage definition |

#### repo (CodeCommit)

| Field | Type | Description |
| :- | :- | :- |
| `type` | _`CodeCommit`_ | **Required**. literal if CodeCommit |
| `name` | `string` | **Required**. name of the repo |
| `create` | `boolean` | whether to create or pre-existing |

#### repo (GitHub)

| Field | Type | Description |
| :- | :- | :- |
| `type` | _`GitHub`_ | **Required**. literal if GitHub |
| `name` | `string` | **Required**. GitHub repo name |
| `tokenName` | `string` | **Required**. Secrets Manager token name |
| `owner` | `string` | **Required**. GitHub account name |

#### build

| Field | Type | Description |
| :- | :- | :- |
| `compute` | _`Small / Medium / Large / 2xLarge`_ | build container compute size |

#### validate

| Field | Type | Description |
| :- | :- | :- |
| `compute` | _`Small / Medium / Large / 2xLarge`_ | validate container compute size |
| `emails` | `array of strings` | email addresses to send notification |
