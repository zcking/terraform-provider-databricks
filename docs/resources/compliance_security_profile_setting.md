---
subcategory: "Settings"
---

# databricks_compliance_security_profile_workspace_setting Resource

The `databricks_compliance_security_profile_workspace_setting` resource allows you to control whether to enable the 
compliance security profile for the current workspace. Enabling it on a workspace is permanent. By default, it is 
turned off. This setting can NOT be disabled once it is enabled.

~> This setting can NOT be disabled once it is enabled.

~> On Azure you need to use [azurerm_databricks_workspace](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/databricks_workspace#compliance_security_profile_enabled-1) resource to configure this setting.

-> This resource can only be used with a workspace-level provider!

## Example Usage

```hcl
resource "databricks_compliance_security_profile_workspace_setting" "this" {
  compliance_security_profile_workspace {
    is_enabled           = true
    compliance_standards = ["HIPAA", "FEDRAMP_MODERATE"]
  }
}
```

## Argument Reference

The resource supports the following arguments:

- `compliance_security_profile_workspace` block with following attributes:
  - `is_enabled` - (Required) Enable the Compliance Security Profile on the workspace
  - `compliance_standards` - (Required) Enable one or more compliance standards on the workspace, e.g. `HIPAA`, `PCI_DSS`, `FEDRAMP_MODERATE`

## Import

This resource can be imported by predefined name `global`:

```hcl
import {
  to = databricks_compliance_security_profile_workspace_setting.this
  id = "global"
}
```

Alternatively, when using `terraform` version 1.4 or earlier, import using the `terraform import` command:

```bash
terraform import databricks_compliance_security_profile_workspace_setting.this global
```
