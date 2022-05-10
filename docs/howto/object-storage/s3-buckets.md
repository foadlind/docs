# Working with S3 buckets

This article shows you how to create buckets, and modify their policies.
To interact with the object storage in {{extra.brand}}, we will use the 
[AWS CLI](https://aws.amazon.com/cli/) command line tool. To configure AWS CLI,
you need S3-compatible access key ID and secret key. 
Follow [this guide](s3-credentials.md) to learn how to obtain these credentials.

## Creating private buckets
You can create a bucket with private Access Control List (ACL) using the 
following command:
```bash
aws --endpoint-url=<region>.{{extra.brand_domain}}:8080 s3api create-bucket --acl private --bucket <bucket-name>
```

## Enabling bucket versioning
To enable versioning in a bucket use the following command:
```bash
aws --endpoint-url=<region>.{{extra.brand_domain}}:8080 s3api put-bucket-versioning --versioning-configuration Status=Enabled --bucket <bucket-name>
```

## Setting object expiry
You can set a bucket's lifecycle configuration such that it automatically 
deletes objects after a certain number of days. 

First, you need to create a JSON file that contains the lifecycle configuration
rule (Make sure to set the `Expiration.Days` attribute to your desired value.):

```bash
cat << EOF > lifecycle.json
{
"Rules": [{
    "ID": "cleanup",
    "Status": "Enabled",
    "Prefix": "",
    "Expiration": {
        "Days": 5
    }
}]
}
EOF
```

Then, apply this lifecycle configuration to your bucket using the command below:

```bash
aws --endpoint-url=<region>.{{extra.brand_domain}}:8080 s3api put-bucket-lifecycle-configuration --lifecycle-configuration file://lifecycle.json --bucket <bucket-name>
```
