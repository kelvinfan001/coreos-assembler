---
layout: default
parent: Mantle
nav_order: 9
---

# Platform Credentials
{: .no_toc }

Each platform reads the credentials it uses from different files. The `aliyun`, `aws`, `azure`, `do`, `esx` and `packet`
platforms support selecting from multiple configured credentials, call "profiles". The examples below
are for the "default" profile, but other profiles can be specified in the credentials files and selected
via the `--<platform-name>-profile` flag:
```
kola spawn -p aws --aws-profile other_profile
```

1. TOC
{:toc}

## aliyun

`aliyun` reads the `~/.aliyun/config.json` file used by Aliyun's aliyun command-line tool.
It can be created using the `aliyun` command:
```
$ aliyun configure
```
To configure a different profile, use the `--profile` flag
```
$ aliyun configure --profile other_profile
```

The `~/.aliyun/config.json` file can also be populated manually:
```
{
  "current": "",
  "profiles": [
    {
      "name": "",
      "mode": "AK",
      "access_key_id": "ACCESS_KEY_ID",
      "access_key_secret": "ACCESS_KEY_SECRET",
      "sts_token": "",
      "ram_role_name": "",
      "ram_role_arn": "",
      "ram_session_name": "",
      "private_key": "",
      "key_pair_name": "",
      "expired_seconds": 0,
      "verified": "",
      "region_id": "eu-central-1",
      "output_format": "json",
      "language": "zh",
      "site": "",
      "retry_timeout": 0,
      "retry_count": 0
    }
  ]
}
```

## aws

`aws` reads the `~/.aws/credentials` file used by Amazon's aws command-line tool.
It can be created using the `aws` command:
```
$ aws configure
```
To configure a different profile, use the `--profile` flag
```
$ aws configure --profile other_profile
```

The `~/.aws/credentials` file can also be populated manually:
```
[default]
aws_access_key_id = ACCESS_KEY_ID_HERE
aws_secret_access_key = SECRET_ACCESS_KEY_HERE
```

To install the `aws` command in the SDK, run:
```
sudo emerge --ask awscli
```

## azure

`azure` uses `~/.azure/azureProfile.json`. This can be created using the `az` [command](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli):
```
$ az login`
```
It also requires that the environment variable `AZURE_AUTH_LOCATION` points to a JSON file (this can also be set via the `--azure-auth` parameter). The JSON file will require a service provider active directory account to be created.

Service provider accounts can be created via the `az` command (the output will contain an `appId` field which is used as the `clientId` variable in the `AZURE_AUTH_LOCATION` JSON):
```
az ad sp create-for-rbac
```

The client secret can be created inside of the Azure portal when looking at the service provider account under the `Azure Active Directory` service on the `App registrations` tab.

You can find your subscriptionId & tenantId in the `~/.azure/azureProfile.json` via:
```
cat ~/.azure/azureProfile.json | jq '{subscriptionId: .subscriptions[].id, tenantId: .subscriptions[].tenantId}'
```

The JSON file exported to the variable `AZURE_AUTH_LOCATION` should be generated by hand and have the following contents:
```
{
  "clientId": "<service provider id>", 
  "clientSecret": "<service provider secret>", 
  "subscriptionId": "<subscription id>", 
  "tenantId": "<tenant id>", 
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com", 
  "resourceManagerEndpointUrl": "https://management.azure.com/", 
  "activeDirectoryGraphResourceId": "https://graph.windows.net/", 
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/", 
  "galleryEndpointUrl": "https://gallery.azure.com/", 
  "managementEndpointUrl": "https://management.core.windows.net/"
}

```

## do

`do` uses `~/.config/digitalocean.json`. This can be configured manually:
```
{
    "default": {
        "token": "token goes here"
    }
}
```

## esx

`esx` uses `~/.config/esx.json`. This can be configured manually:
```
{
    "default": {
        "server": "server.address.goes.here",
        "user": "user.goes.here",
        "password": "password.goes.here"
    }
}
```

## gce

`gce` uses the `~/.boto` file. When the `gce` platform is first used, it will print
a link that can be used to log into your account with gce and get a verification code
you can paste in. This will populate the `.boto` file.

See [Google Cloud Platform's Documentation](https://cloud.google.com/storage/docs/boto-gsutil)
for more information about the `.boto` file.

## openstack

`openstack` uses `~/.config/openstack.json`. This can be configured manually:
```
{
    "default": {
        "auth_url": "auth url here",
        "tenant_id": "tenant id here",
        "tenant_name": "tenant name here",
        "username": "username here",
        "password": "password here",
        "user_domain": "domain id here",
        "floating_ip_pool": "floating ip pool here",
        "region_name": "region here"
    }
}
```

`user_domain` is required on some newer versions of OpenStack using Keystone V3 but is optional on older versions. `floating_ip_pool` and `region_name` can be optionally specified here to be used as a default if not specified on the command line.

## packet

`packet` uses `~/.config/packet.json`. This can be configured manually:
```
{
	"default": {
		"api_key": "your api key here",
		"project": "project id here"
	}
}
```

## qemu

`qemu` is run locally and needs no credentials, but does need to be run as root.

## qemu-unpriv

`qemu-unpriv` is run locally and needs no credentials. It has a restricted set of functionality compared to the `qemu` platform, such as:

- No [Local cluster](platform/local/)
- Usermode networking instead of namespaced networks
  * Single node only, no machine to machine networking
  * Machines have internet access