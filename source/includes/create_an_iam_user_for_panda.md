##IAM user


### Create an IAM user for Panda

If you don't want to enter your master AWS credentials when creating a
new Cloud, here is an alternative method.

*NOTE*: Panda NEVER stores your AWS credentials. We only use them to
create, identify and authorise your S3 bucket to be used by Panda.

Amazon allows you to create restricted users called IAM. Instead of
giving your master AWS credentials you can create an IAM user with just
the permissions that Panda needs to authorise the bucket and feed these
credentials to Panda when creating a new Cloud. For more informations on
IAM have a look at Amazon's documentation here:
[https://console.aws.amazon.com/iam](https://console.aws.amazon.com/iam)

### Create the user

To ensure that your user will be usable by Panda, you need to attach some policies to this user.

* Select your user and click on `Attach user Policy` (You can attach the policy directly to the group if you prefer)

![IAM step 1](/images/docs/iam-step-1.png)

* Select `Policy Generator` -> `Select`

![IAM step 2](/images/docs/iam-step-2.png)

* Select `Effect` => `Allow`, `AWS Service` => `Amazon S3`
* Click on `Action` and tick the following options: `GetBucketAcl`, `PutBucketAcl`, `GetBucketLocation`

> Policy statement

```json
{
  "Statement": [
    {
      "Sid": "StmtXXXXXXXXX",
      "Action": [
        "s3:GetBucketAcl",
        "s3:GetBucketLocation",
        "s3:PutBucketAcl"
      ],
      "Effect": "Allow",
      "Resource": [
        "arn:aws:s3:::*"
      ]
    }
  ]
}
```

![IAM step 3](/images/docs/iam-step-3.png)

* Insert in the resource field: `arn:aws:s3:::bucket-name/*` if you want this policy to be applied to a specific bucket or `arn:aws:s3:::*` for any bucket of your account.
* Click on `Add Statement` and then `Continue`.
* Apply your policy.

<alert aside="success">
  Now your user should be ready.
</alert>
