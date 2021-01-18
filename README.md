# TFEv4 External Services

Sets up the external services resources for TFEv4 deployment according to the [reference architecture](https://www.terraform.io/docs/enterprise/before-installing/reference-architecture/aws.html#infrastructure-diagram).

The module will create

* PostgreSQL RDS
* Security group for the RDS
* Subnet group for the RDS
* S3 bucket

## Requirements

The requirements to use the module are:

* Terraform CLI version `>= 0.13`
* Provider AWS version `~> 3.0`

## Input Variables

The input variables available for the module are in the table below.

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| pg_identifier_prefix | `string` | `"tfe-pg-instance-"` | Prefix to use for the PostgreSQL instance identifier. |
| pg_subnet_ids | `list(string)` | | List of AWS subnet ids to use for PostgreSQL DB subnet group. |
| pg_vpc_id | `string` | | Id of the VPC in which the PostgreSQL instance is being created. Used to create the security group. |
| pg_allow_ingress_cidrs | `list(string)` | | CIDRs to allow ingress traffic from. |
| pg_instance_class | `string` | `"db.m4.large"` | The instance class of the PostgreSQL instance. |
| pg_engine_version | `string` | `"10.13"` | The engine version of the PostgreSQL instance. |
| pg_allocated_storage | `number` | `100` | Storage amount to allocate for the PostgreSQL instance. |
| pg_storage_type | `string` | `gp2` | Storage type used by the PostgreSQL instance. |
| pg_multi_az | `bool` | `true` | Specifies if the PostgreSQL instance is multi-AZ. |
| pg_parameter_group_name | `string` | `null` | Name of the DB parameter group to associate. |
| pg_deletion_protection | `bool` | `false` | If the PostgreSQL instance should have deletion protection enabled. |
| pg_backup_retention_period | `number` | `0` | The days to retain backups for. Must be between 0 and 35. |
| pg_db_name | `string` | `"tfe"` | The name of the database to create when the PostgreSQL instance is created. |
| pg_username | `string` | `"postgres"` | Username for the master PostgreSQL instance user. |
| pg_password | `string` | `"10.13"` | Password for the master PostgreSQL instance user. |
| s3_bucket_name | `string` | `[]` | Name of the s3 bucket to create. |
| s3_force_delete | `bool` | | Whether all objects (including any locked objects) should be deleted from the bucket so that the bucket can be destroyed without error. |
| common_tags | `map(string)` | `{}` | Tags to apply to all resources. |

## Outputs

The outputs declared by the module are in the table below

| Variable | Type | Description |
|----------|------|-------------|
| s3_bucket_name | `string` | The name of the bucket. |
| s3_bucket_region | `string` | The AWS region the bucket resides in. |
| pg_address | `string` | The hostname of the PostgreSQL instance. |

## Example Module Declaration

An example of how the module can be used.

```hcl
module "tfe_external_services" {
  source = "git::https://github.com/tfemodules/terraform-aws-tfev4-external-services.git"

  s3_bucket_name  = "tfe-bucket"
  s3_force_delete = true

  pg_identifier_prefix   = "tfe-pg-instance-"
  pg_subnet_ids          = ["subnet-xxxx", "subnet-yyyy"]
  pg_vpc_id              = "vpc-xxxx"
  pg_allow_ingress_cidrs = ["172.16.0.0/24", "172.16.1.0/24"]
  pg_password            = "pa$$w0rd"

  common_tags = {
    project = "tfe-network-example"
  }
}
```
