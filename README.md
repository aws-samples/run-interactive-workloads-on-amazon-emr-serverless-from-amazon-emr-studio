
## Instructions to deploy the pre-requisite resources EMR Serverless Interactive Applications setup

In order to execute the steps in this section, we logon as the AWS account administrator with permissions to create S3 buckets, IAM users, roles, and policies, VPC, and related resources. Perform the following one-time setup:

1. **Create Amazon S3 bucket to store Workspace notebooks, libraries, etc.**

On Amazon S3 console, follow [these steps](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html) to create an S3 bucket. Make sure you select the right region. We name our bucket: emrserverless-interactive-blog-\<account-id\>-\<region-name\>.

1. **Create VPC to provide the Serverless Application with internet connectivity**

On [VPC Management console](http://console.aws.amazon.com/vpcconsole), choose **Create VPC** and follow the instructions in [Create a VPC](https://docs.aws.amazon.com/vpc/latest/userguide/create-vpc.html). Attach a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) per AZ for your private [subnets](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html) to access internet. Internet connectivity to install 3rd party libraries and an [S3 Gateway endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-s3.html) to access public S3 buckets. Leave everything else as default and create a VPC.

1. **Create EMR Serverless runtime role.**

On IAM console, create an IAM policy using the following policy document. Replace the \<account-id\> and \<region\> with your account number and region. We name the policy as **emr-serverless-s3-runtime-policy**. The permissions in this policy are needed to access sample data from a public S3 bucket and read and write access to your S3 bucket. We also provide access to the [AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/catalog-and-crawler.html), a centralized metadata repository for all your data assets across various data sources,which is commonly needed in most workloads. 
Here is a sample runtime policy: [emr-serverless-s3-runtime-policy](iam-policies/emr-serverless-s3-runtime-policy.json)

Next, create an IAM role named **emr-serverless-runtime-role** using the sample trust policy in[these instructions](https://docs.aws.amazon.com/emr/latest/EMR-Serverless-UserGuide/getting-started.html#gs-runtime-role). This allows jobs submitted to your Amazon EMR Serverless applications to access other AWS services on your behalf. Attach the **emr-serverless-s3-runtime-policy** created to your runtime role.

1. **Create EMR Studio service role**

On IAM console, create an IAM policy named **emr-studio-service-policy** with the following policy document to give read and write access to the S3 bucket created in the pre-requisite step 1. Replace the \<account-id\> and \<region\> with your account number and region.

Here is a sample EMR Studio service policy: [emr-studio-service-policy](iam-policies/emr-studio-service-policy.json)

Create an IAM role named **emr-studio-service-role** with custom trust policy from [these instructions](https://docs.aws.amazon.com/emr/latest/EMR-Serverless-UserGuide/using-service-linked-roles.html). Replace the \<account-id\> and \<region\> with your account number and region. Attach policy document role emr-studio-service-policy created to the role.

1. **Create an EMR administrator user**

EMR Administrator persona creates and configures [EMR Studio](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-configure.html), [EMR Serverless interactive applications](https://docs.aws.amazon.com/emr/latest/EMR-Serverless-UserGuide/studio.html), launches workspaces, and assigns and manages developer users who can use the workspaces to attach applications to and developing notebooks to interact with the serverless application.

Create an IAM policy that allows the admin to create Applications and Workspaces. On IAM console to create an IAM policy named **emrs-interactive-app-admin-policy**. Replace the serverless runtime role and EMR Studio service role if you have used a different name for your roles.

Here is a sample EMR Administrator policy: [emrs-interactive-app-admin-policy](iam-policies/emrs-interactive-app-admin-policy.json)

Create an EMR Studio administrator user with name **emrs-interactive-app-admin-user** , providing management console access, select **I want to create an IAM** user and select **Next**.

In the permissions page, select **Attach the policies directly** and select the **emrs-interactive-app-admin-policy** policy.

![Shape1](RackMultipart20240121-1-12p2f1_html_9b0ea18d9b8950ab.gif)

![](RackMultipart20240121-1-12p2f1_html_7f97f03f8a34a0.png)

Next choose **Create user** to create the EMR Studio administrator.

1. **Create EMR Studio Data Developer user**

An EMR Studio Developer user is a persona that is assigned a workspace and EMR Serverless interactive application by the EMR Administrator persona. This user attaches the right serverless endpoint for their workload and create and work with Jupyter Notebooks within their assigned workspaces. In some organizations, a data developer might be working on multiple projects or might want to test and compare different workloads. In that scenario, the data developer might need the flexibility to create multiple serverless applications and attach and detach them to their workspaces. The policies used this blog provide the [power user permissions](https://docs.aws.amazon.com/emr/latest/EMR-Serverless-UserGuide/security-iam-user-access-policies.html) needed for this scenario for the data developer.

In IAM console, create an EMR Interactive Studio user policy named **emrs-interactive-app-dev-policy** using the sample policy given below (replace \<account-id\>with your AWS account id):

Here is a sample Studio data developer policy: [emrs-interactive-app-dev-polic](iam-policies/emrs-interactive-app-dev-policy.json)

Create an EMR Studio developer user with name **emrs-interactive-app-dev-user** , providing management console access, choose **I want to create an IAM user**. Click Next. **Attach the policies directly** and select the **emrs-interactive-app-dev-policy** policy previously created and create user. Download the credentials for studio user as a CSV file and share it with your Data Developer.

If you are using an IAM federation with an external identity provider (IdP) or IAM Identity Center, attach the above policy to the corresponding IAM roles.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

