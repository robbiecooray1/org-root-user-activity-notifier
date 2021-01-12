# org-root-user-activity-notifier

This non-production sample code enables notifications for root user activity across all accounts and selected regions within a selected OU (or root OU).

The inspiration came from a customer requirement where the customer has created a Control Tower landing zone, and wanting to have a solution to enable notifications for root user activity.

## Prerequisities

1. Multi-region AWS CloudTrail trail needs to be enabled for all AWS regions
1. Enable trusted access between AWS CloudFormation StackSets and AWS Organizations

## Deployment steps

Deployment requires deploying the following items in sequence:

1. Deploy the org-level Cloudformation `Stack`. 

2. Deploy the member-level Cloudformation `StackSet`

3. Deploy stack instances

### Instructions

1. Deploy the `org-level` Stack, wait for stack creation to complete.
    * Include `email address` and the `Organisation ID` as parameters.

<pre>
aws cloudformation create-stack --stack-name org-root-user-activity-notifier --template-body file://org-root-user-activity-notifier.yaml --parameters ParameterKey=RootActivitySNSTopicSubscriptionEmail,ParameterValue=<b><i>&lt;email_address&gt;</i></b> ParameterKey=OrganizationId,ParameterValue=<b><i>&lt;organisation id, i.e. o-xxxxxxxxxx&gt;</i></b>
</pre>

2. You will receive an email to confirm the subscription. Confirm by clicking the link on the email.

3. Deploy the `member-level` StackSet
    * Include the ARN of the SNS topic from the `org-level` stack output.

<pre>
aws cloudformation create-stack-set --stack-set-name member-root-user-activity-notifier --template-body file://member-root-user-activity-notifier.yaml --permission-model SERVICE_MANAGED --auto-deployment Enabled=true,RetainStacksOnAccountRemoval=false --capabilities CAPABILITY_NAMED_IAM --parameters ParameterKey=RootActivitySNSTopic,ParameterValue=<b><i>&lt;Arn of the SNS Topic&gt;</i></b>
</pre>

4. Create stack instances from the `member-level` StackSet.

    NOTE: If you require console sign-in attempts, please include `us-east-1`. This is a global event and only handled in us-east-1.

<pre>
aws cloudformation create-stack-instances --stack-set-name member-root-user-activity-notifier --regions <b><i>&lt;list or regions separated by space, i.e. us-east-1 us-east-2&gt;</i></b> --deployment-targets OrganizationalUnitIds=<b><i>&lt;root org unit id, i.e. r-xxxx&gt;</i></b>
</pre>

## Considerations

1. Add stack instances based on new regions that will be made available in the future.

## Clean up

Follow these steps to remove the solution from your environment.

1. Remove member stack instances and stack set
<pre>
aws cloudformation delete-stack-instances --stack-set-name member-root-user-activity-notifier --no-retain-stacks --regions <b><i>&lt;list or regions separated by space, i.e. us-east-1 us-east-2&gt;</i></b> --deployment-targets OrganizationalUnitIds=<b><i>&lt;root org unit id, i.e. r-xxxx&gt;</i></b>
</pre>

<pre>
aws cloudformation delete-stack-set --stack-set-name member-root-user-activity-notifier
</pre>

2. Remove management stack
<pre>
aws cloudformation delete-stack --stack-name org-root-user-activity-notifier
</pre>