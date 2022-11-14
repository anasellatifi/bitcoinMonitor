# Bitcoin Monitor

This is an ETL pipeline to pull bitcoin exchange data from [CoinCap API](https://docs.coincap.io/) and load it into our data warehouse.

## Architecture

![Arch](assets/images/bc_arch.png)

We use python to pull, transform and load data. Our warehouse is postgres. We also spin up a Metabase instance for our presentation layer.

All of the components are running as docker containers.

## Dashboard
A sample dashboard using bitcoin exchange data is shown below.

![Dash](assets/images/bc_dash.png)

## Setup

### Pre-requisites

1. [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
2. [Github account](https://github.com/)
3. [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli) 
4. [AWS account](https://aws.amazon.com/) 
5. [AWS CLI installed](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) and [configured](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)
6. [Docker](https://docs.docker.com/engine/install/) with at least 4GB of RAM and [Docker Compose](https://docs.docker.com/compose/install/) v1.27.0 or later

Run these commands to setup the project locally and on the cloud.

```shell
# Clone the code as shown below.
git clone https://github.com/anasellatifi/bitcoinWatcher.git
cd bitcoinWatcher

# Local run & test
make up # start the docker containers on your computer & runs migrations under ./migrations
make ci # Runs auto formatting, lint checks, & all the test files under ./tests

# Create AWS services with Terraform
make tf-init # Only needed on your first terraform run (or if you add new providers)
make infra-up # type in yes after verifying the changes TF will make

# Wait until the EC2 instance is initialized, you can check this via your AWS UI
# See "Status Check" on the EC2 console, it should be "2/2 checks passed" before proceeding

make cloud-metabase # this command will forward Metabase port from EC2 to your machine and opens it in the browser
```

```shell
make db-migration # enter a description, e.g., create some schema
# make your changes to the newly created file under ./migrations
make warehouse-migration # to run the new migration on your warehouse
```

### Tear down infra

After you are done, make sure to destroy your cloud infrastructure.

```shell
make down # Stop docker containers on your computer
make infra-down # type in yes after verifying the changes TF will make
```