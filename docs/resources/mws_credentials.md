---
subcategory: "Deployment"
---
# databricks_mws_credentials Resource

This resource to configure the cross-account role for creation of new workspaces within AWS.

-> This resource can only be used with an account-level provider!

Please follow this [complete runnable example](../guides/aws-workspace.md) with new VPC and new workspace setup. Please pay special attention to the fact that there you have two different instances of a databricks provider - one for deploying workspaces (with `host="https://accounts.cloud.databricks.com/"`) and another for the workspace you've created with `databricks_mws_workspaces` resource. If you want both creation of workspaces & clusters within workspace within the same terraform module (essentially same directory), you should use the provider aliasing feature of Terraform. We strongly recommend having one terraform module for creation of workspace + PAT token and the rest in different modules.

## Example Usage

```hcl
variable "databricks_account_id" {
  description = "Account Id that could be found in the top right corner of https://accounts.cloud.databricks.com/"
}

variable "prefix" {
  description = "Names of created resources will be prefixed with this value"
}

data "databricks_aws_assume_role_policy" "this" {
  external_id = var.databricks_account_id
}

resource "aws_iam_role" "cross_account_role" {
  name               = "${var.prefix}-crossaccount"
  assume_role_policy = data.databricks_aws_assume_role_policy.this.json
  tags               = var.tags
}

data "databricks_aws_crossaccount_policy" "this" {
}

resource "aws_iam_role_policy" "this" {
  name   = "${var.prefix}-policy"
  role   = aws_iam_role.cross_account_role.id
  policy = data.databricks_aws_crossaccount_policy.this.json
}

resource "databricks_mws_credentials" "this" {
  provider         = databricks.mws
  credentials_name = "${var.prefix}-creds"
  role_arn         = aws_iam_role.cross_account_role.arn
}
```

## Argument Reference

The following arguments are required:

* `account_id` - **(Deprecated)** Maintained for backwards compatibility and will be removed in a later version. It should now be specified under a provider instance where `host = "https://accounts.cloud.databricks.com"` 
* `credentials_name` - (Required) name of credentials to register
* `role_arn` - (Required) ARN of cross-account role

## Attribute Reference

In addition to all arguments above, the following attributes are exported:

* `id` - Canonical unique identifier for the mws credentials.
* `creation_time` - (Integer) time of credentials registration
* `credentials_id` - (String) identifier of credentials

## Import

This resource can be imported by the combination of its identifier and the account id:

```hcl
import {
  to = databricks_mws_credentials.this
  id = "<account_id>/<credentials_id>"
}
```

Alternatively, when using `terraform` version 1.4 or earlier, import using the `terraform import` command:

```bash
terraform import databricks_mws_credentials.this <account_id>/<credentials_id>
```

## Related Resources

The following resources are used in the same context:

* [Provisioning Databricks on AWS](../guides/aws-workspace.md) guide.
* [databricks_mws_customer_managed_keys](mws_customer_managed_keys.md) to configure KMS keys for new workspaces within AWS.
* [databricks_mws_log_delivery](mws_log_delivery.md) to configure delivery of [billable usage logs](https://docs.databricks.com/administration-guide/account-settings/billable-usage-delivery.html) and [audit logs](https://docs.databricks.com/administration-guide/account-settings/audit-logs.html).
* [databricks_mws_networks](mws_networks.md) to [configure VPC](https://docs.databricks.com/administration-guide/cloud-configurations/aws/customer-managed-vpc.html) & subnets for new workspaces within AWS.
* [databricks_mws_storage_configurations](mws_storage_configurations.md) to configure root bucket new workspaces within AWS.
* [databricks_mws_workspaces](mws_workspaces.md) to set up [AWS and GCP workspaces](https://docs.databricks.com/getting-started/overview.html#e2-architecture-1).

