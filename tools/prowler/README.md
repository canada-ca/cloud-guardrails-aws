# GC Accelerators (AWS Guardrails – Prowler Extensions)

This project includes extensions to Prowler for verifying the Government of Canada’s Enterprise Guardrails for AWS. The Prowler project can be found [here](https://github.com/toniblyx/prowler).

Prowler is written in bash and makes calls to AWS APIs using the AWS CLI to verify the configuration of cloud environments. It is super easy to extend once you know how. Prowler, as delivered, provides verification against compliance verticals such as the Center for Internet Security (CIS) benchmarks, HIPPA, and others. 

Prowler performs a series of checks reporting on the success or failure of those checks. Checks are stored as individual bash scripts in the /checks sub-directory. New scripts to cover the GC Guardrails can be copied in the /checks sub-directory. 

Prowler also groups checks by compliance verticals (CIS, HIPAA, etc..). New groups can be added by adding a new bash script to the /groups sub-directory. A new scripts to create a group specifically for the GC Guardrails can be copied in the /groups sub-directory. 

## Instructions:
1. Follow the instructions for installing Prowler, including the AWS CLI pre-requisites
2. Copy the checks in the /checks folder from this project into the /prowler/checks folder
3. Copy the group file in the /gorups folder from this project into the /prowler/groups folder

To run Prowler to test for the GC Guardrails only, run the following command:


```sh
$ ./prowler –g gc
```

## Checks
The ‘gc’ group of checks include the following checks:

| GC Guardrail | Description | File |
| ------ | ------ | ------ |
| 3.0 | Validate that there is no access key for the AWS account’s root user. The access key for the AWS account root user gives full access to all resources for all AWS services, including billing information. You cannot reduce the permissions associated with your AWS account root user access key.  | check112  |
| 3.1 | Confirm with the partner department that they have secured their AWS account’s root user password (for access to the AWS Management Console). Verify breakglass procedure manually. | NOT AUTOMATED |
| 3.2 | Confirm that they are using a strong password and are not sharing it with anyone. Verify a strong password or strong password policy is enabled. According to GC password guidance this is maximum password history (24 is the highest value in AWS) and a password length of 12 or longer | check_gcextra32, check110 |
| 3.3 | Look for failed compliance status against the rule “Ensure MFA is enabled for the "root" account”.  Ensure MFA is enabled for the root account under the organization.  | check113 |
| 6.1a | Verify that the “SSC-RoleManagementPolicy” policy exists |  |
| 6.1b | Verify that the “SSC-RoleManagementPolicy” policy exists |  |
| 6.2 | Verify that the “SSC-CloudBroker” role has been created. |  |
| 6.3 | Verify that the SSC account is a trusted entity of the “SSC-CloudBroker” role. NOTE: The AWS account is not hard-coded into the script and must be added before execution. |  |
| 6.4 | Verify that the “SSC-CloudBroker” role contains the policies “SSC-RoleManagementPolicy”, “Billing”, “BillingAndAccountAccess”, “AWSPrivateMarketplaceAdminFullAccess”, “AWSMarketplaceRead-only”, “AWSCloudFormationReadOnlyAccess”, and “AWSHealthFullAccess” |  |
| 7.0 | Verify that all EC2 instances are deployed only within the AWS Canada Central Region. Can be implemented via System Control Policy in Landing Zone.  |  |
| 8.0 | Verify that storage instances are configured with encryption enabled. |  |
| 13.0 | Validate that the AWS Organizations master account is utilizing Government of Canada Private Marketplace which only contains GC-approved cloud marketplace products and that it is shared across AWS Organizations.  | NOT AUTOMATED |
| 13.1 | Validate that there is an SCP applied to all OUs that prevents any access to the Private Marketplace administration page; note that the only exception is for SSC’s possible access to add/remove products from the Private Marketplace over time. |  |


## Roadmap
The intent is to grow the number of GC Guardrails tested over time. 
1.	Increase usage of AWS Organizations to determine the collection of accounts used by a department is being worked on. Also, being able to identify how those accounts map back to the GC Accelerators architecture is on the roadmap. This will allow the number of GC Guardrail being verified to increase.

2.	Multi-account verification. There are capabilities within Prowler. They need to be explored, or simply accomplish this with AWS Security Hub. Ideally however, you provide the master account in AWS Organizations and it then verifies that account and all others in the org.

3.	Integration with AWS Security Hub. An architecture already exists [here](https://aws.amazon.com/blogs/security/use-aws-fargate-prowler-send-security-configuration-findings-about-aws-services-security-hub/)

4.	Move away from Prowler by going cloud native be using lambda. This is in the long-term future. The CIS benchmarks in AWS Security Hub verify some of the GC Guardrails. Lambda functions with AWS Security Hub integrations could be used for the rest.
