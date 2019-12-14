# GC Accelerators (AWS Guardrails – Prowler Extensions)

This project includes extensions to Prowler for verifying the Government of Canada’s Enterprise Guardrails for AWS. The Prowler project can be found [here](https://github.com/toniblyx/prowler).

Prowler is written in bash and makes calls to AWS APIs using the AWS CLI to verify the configuration of cloud environments. It is super easy to extend once you know how. Prowler, as delivered, provides verification against compliance verticals such as the Center for Internet Security (CIS) benchmarks, HIPPA, and others. 

Prowler performs a series of checks reporting on the success or failure of those checks. Checks are stored as individual bash scripts in the /checks sub-directory. New scripts to cover the GC Guardrails can be copied in the /checks sub-directory. 

Prowler also groups checks by compliance verticals (CIS, HIPAA, etc..). New groups can be added by adding a new bash script to the /groups sub-directory. A new scripts to create a group specifically for the GC Guardrails can be copied in the /groups sub-directory. 

## Instructions:
1. Follow the instructions for installing Prowler, including the AWS CLI pre-requisites
2. Copy the checks in the /checks folder from this project into the /prowler/checks folder
3. Copy the group file in the /groups folder from this project into the /prowler/groups folder

To run Prowler to test for the GC Guardrails only, run the following command:


```sh
$ ./prowler –g gc
```

## Checks
The ‘gc’ group of checks include the following checks:

| GC Guardrail | Description | Check File |
| ------ | ------ | ------ |
| [01](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/01_Protect-Root-Account.md) | Validate that there is no access key for the AWS account’s root user. The access key for the AWS account root user gives full access to all resources for all AWS services, including billing information. You cannot reduce the permissions associated with your AWS account root user access key.  | check112  |
| [01](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/01_Protect-Root-Account.md) | Look for failed compliance status against the rule “Ensure MFA is enabled for the "root" account”.  Ensure MFA is enabled for the root account under the organization.  | check113 |
| [01](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/01_Protect-Root-Account.md) | Using AWS IAM, validate that there is no access key for the AWS account’s root user. |  |
| [01](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/01_Protect-Root-Account.md) | Confirm with the department that they have changed the AWS account’s root user password (for access to the AWS Management Console) after receiving it from SSC using a strong password. |  |
| [01](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/01_Protect-Root-Account.md) | Confirm the password and MFA token are locked in a safe or other secure location, in accordance with the Department's approved Emergency Break Glass Procedure. |  |
| [02](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/02_Management-Admin-Privileges.md) | Confirm that the password policy aligns with GC Password Guidance. According to GC password guidance, passwords should be at least 12 characters and historical reuse should be set as high as possible. For AWS historical reuse maximum is 24.| check_extragc32, check110  |
| [02](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/02_Management-Admin-Privileges.md),[03](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/02_Management-Admin-Privileges.md)| MFA is included in the GC Password Guidance. Using IAM, ensure that all Identity and Access Management users have an assigned MFA device for Security Credentials. If an IAM user does not have an MFA device assigned, confirm that the user only requires programmatic access and does not have console management access by validating the user’s configured security credentials.| check12 |
| [04](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/04_Enterprise-Monitoring-Accounts.md)| Verify that the “SSC-RoleManagementPolicy” policy exists |  |
| [04](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/04_Enterprise-Monitoring-Accounts.md)|Verify that the “BillingAndAccountAccess”” policy exis ts |  |
| [04](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/04_Enterprise-Monitoring-Accounts.md)| Verify that the “SSC-CloudBroker” role has been created. | check_extragc62 |
| [04](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/04_Enterprise-Monitoring-Accounts.md)|  Verify that the SSC account is a trusted entity of the “SSC-CloudBroker” role. NOTE: The AWS account is not hard-coded into the script and must be added before execution. | check_extragc63 |
| [04](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/04_Enterprise-Monitoring-Accounts.md)| Verify that the “SSC-CloudBroker” role contains the policies “SSC-RoleManagementPolicy”, “Billing”, “BillingAndAccountAccess”, “AWSPrivateMarketplaceAdminFullAccess”, “AWSMarketplaceRead-only”, “AWSCloudFormationReadOnlyAccess”, and “AWSHealthFullAccess” |  |
| [05](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/05_Data-Location.md)| Verify that all EC2 instances are deployed only within the AWS Canada Central Region. |  |
| [06](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/06_Protect-Data-at-Rest.md)| Verify that storage instances are configured with encryption enabled. | check_extra729 |
| [09](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/09_Network-Security-Services.md)| Look for Internet Gateways (IGWs) attached to a VPC that are outside of the perimeter account for the Landing Zone. | |
| [12](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/12_Cloud-Marketplace-Config.md)| Validate that the AWS Organizations master account is utilizing Government of Canada Private Marketplace which only contains GC-approved cloud marketplace products and that it is shared across AWS Organizations.  | NOT AUTOMATED |
| [12](https://github.com/canada-ca/cloud-guardrails/blob/master/EN/12_Cloud-Marketplace-Config.md)| Validate that there is an SCP applied to all OUs that prevents any access to the Private Marketplace administration page; note that the only exception is for SSC’s possible access to add/remove products from the Private Marketplace over time. |  |


## Roadmap
The intent is to grow the number of GC Guardrails tested over time. 
1.	Increase usage of AWS Organizations to determine the collection of accounts used by a department is being worked on. Also, being able to identify how those accounts map back to the GC Accelerators architecture is on the roadmap. This will allow the number of GC Guardrail being verified to increase.

2.	Multi-account verification. There are capabilities within Prowler. They need to be explored, or simply accomplish this with AWS Security Hub. Ideally however, you provide the master account in AWS Organizations and it then verifies that account and all others in the org.

3.	Integration with AWS Security Hub. An architecture already exists [here](https://aws.amazon.com/blogs/security/use-aws-fargate-prowler-send-security-configuration-findings-about-aws-services-security-hub/)

4.	Move away from Prowler by going cloud native be using lambda. This is along-term future consideration. The CIS benchmarks in AWS Security Hub verify some of the GC Guardrails. Lambda functions with AWS Security Hub integrations could be used for the rest.


