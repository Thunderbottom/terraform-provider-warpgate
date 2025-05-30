---
page_title: "warpgate_user Data Source - terraform-provider-warpgate"
subcategory: ""
description: |-
  Retrieves information about a specific user in WarpGate.
---

# warpgate_user (Data Source)

Retrieves information about a specific user in WarpGate.

## Example Usage

```hcl
data "warpgate_user" "eugene" {
  id = "12345678-1234-1234-1234-123456789012"
}

output "username" {
  value = data.warpgate_user.eugene.username
}

output "ssh_credential_requirements" {
  value = try(data.warpgate_user.eugene.credential_policy[0].ssh, [])
}

# Use the data source to reference an existing user
resource "warpgate_user_role" "eugene_developer" {
  user_id = data.warpgate_user.eugene.id
  role_id = warpgate_role.developers.id
}
```

## Argument Reference

The following arguments are supported:

* `id` - (Required) The ID of the user to look up.

## Attribute Reference

In addition to the arguments listed above, the following attributes are exported:

* `username` - The username of the user.
* `description` - The description of the user.
* `credential_policy` - The credential policy for the user. This is a list with a single element.
  * `http` - List of credential types required for HTTP access.
  * `ssh` - List of credential types required for SSH access.
  * `mysql` - List of credential types required for MySQL access.
  * `postgres` - List of credential types required for PostgreSQL access.

## Working with Credential Policies

When working with credential policies from the data source, you need to account for the fact that the policy might not be set. Here's an example of safely accessing credential policy properties:

```hcl
locals {
  # Safely extract credential policy values with defaults
  ssh_credentials      = try(data.warpgate_user.eugene.credential_policy[0].ssh, [])
  http_credentials     = try(data.warpgate_user.eugene.credential_policy[0].http, [])
  mysql_credentials    = try(data.warpgate_user.eugene.credential_policy[0].mysql, [])
  postgres_credentials = try(data.warpgate_user.eugene.credential_policy[0].postgres, [])
  
  # Check if specific credential types are required
  requires_password   = contains(local.ssh_credentials, "Password")
  requires_public_key = contains(local.ssh_credentials, "PublicKey")
}
```

<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `id` (String) The ID of the user

### Read-Only

- `credential_policy` (List of Object) The credential policy for the user (see [below for nested schema](#nestedatt--credential_policy))
- `description` (String) The description of the user
- `username` (String) The username of the user

<a id="nestedatt--credential_policy"></a>
### Nested Schema for `credential_policy`

Read-Only:

- `http` (List of String)
- `mysql` (List of String)
- `postgres` (List of String)
- `ssh` (List of String)
