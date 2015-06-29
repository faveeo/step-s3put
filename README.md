# s3put

This step app is a modification of wercker/step-s3sync to do a `put` instead of a `sync`

It uses the command `s3cmd put` to copy a folder or a file.
Use the option `opts --recursive` to copy a folder with all it's files recursively into the S3 bucket. See http://s3tools.org/usage 

The synchronized files will get an public access level.

It is recommended that you use application and deployment variables in wercker, so you don't include any private keys in your code.

[![wercker status](https://app.wercker.com/status/661012f3ce5f9c5ecd1c940ebae0da67/m "wercker status")](https://app.wercker.com/project/bykey/661012f3ce5f9c5ecd1c940ebae0da67)

# What's new

- Always display s3cmd output

# Options

* `key-id` (required) The Amazon Access key that will be used for authorization.
* `key-secret` (required) The Amazon Access secret that will be used for authorization.
* `bucket-url` (required) The url of the bucket to sync to, like: `s3://wercker.com`
* `source-dir` (optional, default: `./`) The directory to copy to the remote bucket.
* `opts` (optional, default: `--acl-public`) Arbitrary options provided to s3cmd. See `s3cmd --help` for more.

# Example

```
deploy:
    steps:
        - s3sync:
            key-id: $KEY
            key-secret: $SECRET
            bucket-url: $BUCKET
            source-dir: $SOURCE
            opts: --acl-private
```

# Privacy

By default s3sync will make the files publicly available from your S3 bucket, but you can use the "opts" option to manually specify an ACL option to s3cmd. At the time of writing, these are:

```
  -P, --acl-public      Store objects with ACL allowing read for anyone.
  --acl-private         Store objects with default ACL allowing access for you
                        only.
  --acl-grant=PERMISSION:EMAIL or USER_CANONICAL_ID
                        Grant stated permission to a given amazon user.
                        Permission is one of: read, write, read_acp,
                        write_acp, full_control, all
```

# Permissions

It's a good idea to create a IAM user which just has enough permissions to be able to sync to the S3 buckets that it needs to. The IAM user needs the following permissions to be able to sync a bucket (replace `[bucket_name]` with the bucket name):

```
{
  "Statement": [
    {
      "Action": [
        "s3:DeleteObject",
        "s3:GetObject",
        "s3:GetObjectAcl",
        "s3:ListBucket",
        "s3:PutObject",
        "s3:PutObjectAcl"
      ],
      "Effect": "Allow",
      "Resource": [
          "arn:aws:s3:::[bucket_name]",
          "arn:aws:s3:::[bucket_name]/*"
      ]
    }
  ]
}
```

# License

The MIT License (MIT)

# Changelog

## 3.0.1

Update s3cmd

## 3.0.0

- Fork from https://github.com/wercker/step-s3sync
- Change `sync` to `put` 

## 2.0.3

- Always display s3cmd output

## 2.0.2

- Fix `source-dir` bug

## 2.0.0

- Refactor run.sh
- Update s3cmd to 1.5.1.2
- Bundle s3cmd in step

## 1.1.0

- Add `delete-removed` parameter

## 1.0.0

- Add information about permissions to the README

## 0.0.3

- Initial release
