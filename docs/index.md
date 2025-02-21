---
# generated by https://github.com/hashicorp/terraform-plugin-docs
page_title: "Opensearch Provider"
subcategory: ""
description: |-
  The provider is used to interact with the resources supported by Opensearch.
---

# Opensearch Provider

The provider is used to interact with the resources supported by Opensearch.
The provider needs to be configured with an endpoint URL before it can be
used.

AWS Opensearch Service domains and OpenSearch clusters deployed on Kubernetes
and other infrastructure are supported.

Use the navigation to the left to read about the available resources.

```terraform
# Configure the Opensearch provider
provider "opensearch" {
  url = "http://127.0.0.1:9200"
}

# Create an index template
resource "opensearch_index_template" "template_1" {
  name = "template_1"
  body = <<EOF
{
  "template": "te*",
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "type1": {
      "_source": {
        "enabled": false
      },
      "properties": {
        "host_name": {
          "type": "keyword"
        },
        "created_at": {
          "type": "date",
          "format": "EEE MMM dd HH:mm:ss Z YYYY"
        }
      }
    }
  }
}
EOF
}
```

<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `url` (String) Opensearch URL

### Optional

- `aws_access_key` (String) The access key for use with AWS opensearch Service domains
- `aws_assume_role_arn` (String) Amazon Resource Name of an IAM Role to assume prior to making AWS API calls.
- `aws_assume_role_external_id` (String) External ID configured in the IAM policy of the IAM Role to assume prior to making AWS API calls.
- `aws_profile` (String) The AWS profile for use with AWS opensearch Service domains
- `aws_region` (String) The AWS region for use in signing of AWS opensearch requests. Must be specified in order to use AWS URL signing with AWS OpenSearch endpoint exposed on a custom DNS domain.
- `aws_secret_key` (String) The secret key for use with AWS opensearch Service domains
- `aws_signature_service` (String) AWS service name used in the credential scope of signed requests to opensearch.
- `aws_token` (String) The session token for use with AWS opensearch Service domains
- `cacert_file` (String) A Custom CA certificate
- `client_cert_path` (String) A X509 certificate to connect to opensearch
- `client_key_path` (String) A X509 key to connect to opensearch
- `healthcheck` (Boolean) Set the client healthcheck option for the opensearch client. Healthchecking is designed for direct access to the cluster.
- `host_override` (String) If provided, sets the 'Host' header of requests and the 'ServerName' for certificate validation to this value. See the documentation on connecting to opensearch via an SSH tunnel.
- `insecure` (Boolean) Disable SSL verification of API calls
- `opensearch_version` (String) opensearch Version
- `password` (String) Password to use to connect to opensearch using basic auth
- `sign_aws_requests` (Boolean) Enable signing of AWS opensearch requests. The `url` must refer to AWS ES domain (`*.<region>.es.amazonaws.com`), or `aws_region` must be specified explicitly.
- `sniff` (Boolean) Set the node sniffing option for the opensearch client. Client won't work with sniffing if nodes are not routable.
- `token` (String) A bearer token or ApiKey for an Authorization header, e.g. Active Directory API key.
- `token_name` (String) The type of token, usually ApiKey or Bearer
- `username` (String) Username to use to connect to opensearch using basic auth
- `version_ping_timeout` (Number) Version ping timeout in seconds

## Authentication

### AWS authentication

The provider is flexible in the means of providing credentials for authentication with AWS OpenSearch domains. The following methods are supported, in this order, and explained below:

- Static credentials
- Assume role configuration
- Environment variables
- Shared credentials file

If a [custom domain](https://docs.aws.amazon.com/opensearch-service/latest/developerguide/customendpoint.html) is being used (instead of the default, of the form `https://search-mydomain-1a2a3a4a5a6a7a8a9a0a9a8a7a.us-east-1.es.amazonaws.com`), please make sure to set `aws_region` in the provider configuration.

#### Static credentials

##### IAM user management

If your AWS OpenSearch domain uses IAM user management, static credentials can be provided by adding an `aws_access_key` and `aws_secret_key` in-line in the provider block. If applicable, you may also specify a `aws_token` value.

Example usage:

```tf
provider "opensearch" {
    url            = "https://search-foo-bar-pqrhr4w3u4dzervg41frow4mmy.us-east-1.es.amazonaws.com"
    aws_access_key = "anaccesskey"
    aws_secret_key = "asecretkey"
    aws_token      = "" # if necessary
}
```

##### HTTP basic auth

If your AWS OpenSearch domain uses an internal user database, static credentials can be provided by adding a `username` and `password` in-line in the provider block. Note: You will need to explicitly disabled request signing.

Example usage:

```tf
provider "opensearch" {
    url               = "https://search-foo-bar-pqrhr4w3u4dzervg41frow4mmy.us-east-1.es.amazonaws.com"
    username          = "ausername"
    password          = "apassword"
    # Must be disabled for basic auth
    sign_aws_requests = false
}
```

#### Assume role configuration

You can instruct the provider to assume a role in AWS before interacting with the cluster by setting the `aws_assume_role_arn` variable.
When necessary, use the aws_assume_role_external_id to pass the extenral ID configured in the policy of the role for the provider to assume the role. 

Example usage:

```tf
provider "opensearch" {
    url                         = "https://search-foo-bar-pqrhr4w3u4dzervg41frow4mmy.us-east-1.es.amazonaws.com"
    aws_assume_role_arn         = "arn:aws:iam::012345678901:role/rolename"
    aws_assume_role_external_id = "SecretID"
}
```

#### Environment variables

You can provide your credentials via the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`, environment variables, representing your AWS Access Key and AWS Secret Key. If applicable, the `AWS_SESSION_TOKEN` environment variables is also supported.

Example usage:

```shell
$ export AWS_ACCESS_KEY_ID="anaccesskey"
$ export AWS_SECRET_ACCESS_KEY="asecretkey"
$ terraform plan
```

#### AWS profile

You can specify a named profile that will be used for credentials (either static, or sts assumed role creds).  eg:

```tf
provider "opensearch" {
    url         = "https://search-foo-bar-pqrhr4w3u4dzervg41frow4mmy.us-east-1.es.amazonaws.com"
    aws_profile = "profilename"
}
```

#### Shared Credentials file

You can use an AWS credentials file to specify your credentials. The default location is `$HOME/.aws/credentials` on Linux and macOS, or `%USERPROFILE%\.aws\credentials` for Windows users.

Please refer to the official [userguide](https://docs.aws.amazon.com/cli/latest/userguide/cli-config-files.html) for instructions on how to create the credentials file.

### Connecting to a cluster via an SSH Tunnel

If you need to connect to a cluster via an SSH tunnel (for example, to an AWS VPC Cluster), set the following configuration options in your provider:

```tf
provider "opensearch" {
  url           = "https://localhost:9999" # Replace 9999 with the port your SSH tunnel is running on
  host_override = "vpc-<******>.us-east-1.es.amazonaws.com"
}
```

The `host_override` flag will set the `Host` header of requests to the cluster and the `ServerName` used for certificate validation. It is recommended to set this flag instead of `insecure = true`, which causes certificate validation to be skipped. Note that if both `host_override` and `insecure = true` are set, certificate validation will be skipped and the `Host` header will be overridden.
