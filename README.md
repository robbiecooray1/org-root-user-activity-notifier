# org-root-user-activity-notifier

This non-production sample code enables notifications for root user activity across all accounts and selected regions within a selected OU (or root OU).

The inspiration came from a customer requirement where the customer has created a Control Tower landing zone, and wanting to have a solution to enable notifications for root user activity.

## Prerequisities

1. Multi-region AWS CloudTrail trail needs to be enabled for all AWS regions
1. Enable trusted access between AWS CloudFormation StackSets and AWS Organizations

### Instructions

1. Deploy the stack. Include the following parameters
    * `Email address` - the email address of the receipient
    * `Organisation ID` - the ID of the AWS Organisation
    * `Root Org Unit ID` - The ID of the root OU
    * `Regions` - comma separated list of regions

<pre>
aws cloudformation create-stack --stack-name org-root-user-activity-notifier --template-body file://org-root-user-activity-notifier.yaml --parameters ParameterKey=RootActivitySNSTopicSubscriptionEmail,ParameterValue=<b><i>&lt;email_address&gt;</i></b> ParameterKey=OrganizationId,ParameterValue=<b><i>&lt;organisation id, i.e. o-xxxxxxxxxx&gt;</i></b> ParameterKey=RootOrgUnitId,ParameterValue=<b><i>&lt;Org Unit ID, i.e. r-xxxx&gt;</i></b>  ParameterKey=Regions,ParameterValue=<b><i>&lt;comma separated list of regions i.e. us-east-1,us-east-2&gt;</i></b>
</pre>

2. You will receive an email to confirm the subscription. Confirm by clicking the link on the email.

## Considerations

Update the stack based on new regions that will be made available in the future.

## Clean up

Delete the stack.


<pre>
aws cloudformation delete-stack --stack-name org-root-user-activity-notifier
</pre>