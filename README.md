# Identity & Access Management (IAM) with AWS

<div align="center">
  
  ![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/IAM%20Architecture.png)
  
</div>

## Technologies Utilized

- AWS EC2 Instances
- AWS Identity & Access Management (IAM)
- JSON policy
- AWS Account Alias
- IAM User Groups
- IAM Users
- IAM Policy Simulator

<br />

## What is AWS IAM?

AWS IAM is the identity and access management service within AWS. AWS IAM is used to manage the access level that other users and services have to a given resource.

In this project, I used AWS IAM to manage the access of IAM users within an IAM user group to the EC2 instances I had launched.

<br />

## Tags

Tags are labels used to organize AWS resources. Tags help identify all resources with a given tag easily, making it a useful filter when searching for something. They also help with cost allocation and applying policies based on environment types.

The tag I’ve used on my EC2 instances is called "Env", for Environment. The "Name" tag is assigned by default. The values I’ve assigned for my instances under the "Env" tag are "production" and "development" for each of the respective instances, as well as the actual instance names under the "Name" tag.

![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/Tags.png)

<br />

## IAM Policies

IAM Policies are rules set in place to determine who is able to do what with a set of AWS resources. Permissions are set for IAM users, groups, or roles as to what they can or cannot do on certain resources and when those rules come into play.

### The policy I set up

For this project, I’ve set up a policy using JSON rather than the visual editor to get familiar with the structure of JSON policies.

I’ve created a policy that allows all actions for resources tagged Env - development, allows describing for all EC2 instances, and denies the ability to create or delete tags for all instances.

### JSON Policies: Effect, Action, and Resource

The Effect, Action, and Resource attributes of a JSON policy are the parameters used to determine the scope of the policy

The Effect is an Allow/Deny statement that is applied to a specified Action (e.g. creating tags, describing EC2 instances, etc...) for Resources within a defined scope.

> <b>Bonus:</b> A Condition Block can be applied to define required circumstances for the policy to take effect.

This was used in the first policy (see below) to set a condition that all actions are allowed on any (*) EC2 instances/resources with the condition that the resource is tagged Env - development.

![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/JSON%20Policy.png)

<br />

## Account Alias

An account alias is a simpler name for your AWS account to use instead of your account ID (typically a bunch of numbers) to sign in to the AWS Management Console.

Creating an account alias only takes a few seconds to create. In this project, I made a new AWS console sign-in URL (which will be useful later on) using the account alias I created: https://mshahwan-alias.signin.aws.amazon.com/console/

It is a simpler and more user-friendly URL that can be shared with users as needed.

<br />

## IAM Users and User Groups

### Users

IAM users are the users that will be granted access (based on the permissions we set) to the resources and AWS account. An IAM user will have their own login credentials (username & password) to sign in to the AWS account and access resources.

![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/IAM%20User.png)

### User Groups

IAM user groups are collections of IAM users that are bunched together ("groups"), allowing us to manage the permissions of all the IAM users in a given group at the same time by applying policies to the group instead of to each individual IAM user separately.

I attached the JSON policy I had created to this user group, which means that IAM users in the group are subject to the IAM policies (allow all EC2 actions in Env - dev tagged resources, allow describing for all EC2 instances, and deny all create/delete tag actions) previously configured.

![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/IAM%20User%20Group.png)

<br />

## Logging in as an IAM User

The first way is to sign in using the new console sign-in URL we previously created: https://mshahwan-alias.signin.aws.amazon.com/console

The second way would be to sign in using the AWS command line interface (CLI), SDKs, or APIs.

Once logged in as an IAM user, we can see that a number of access panels on the dashboard say "Access denied", since I was logged in as the IAM user.

![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/IAM%20User%20Dahsboard.png)

<br />

## Testing IAM Policies

I tested my JSON IAM policy by attempting to stop each of the instances using the Instance state settings.

### Stopping the production instance

When I tried to stop the <b>production</b> instance, an error occurred stating that it had "Failed to stop the instance" because the IAM user was "not authorized to perform this operation".

![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/Error.png)

### Stopping the development instance

Next, when I tried to stop the <b>development</b> instance, I was able to successfully stop the instance. This confirmed that the JSON IAM policy worked, as the IAM user would only be allowed to stop an instance for resources tagged Env - development.

![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/Success.png)

## A More Practical Approach

To test the IAM policies, I had to shut down the development instance, which is not practical in a real-world scenario. This would cause a disruption in the development environment, which we should try to avoid.

Instead, a more practical approach is to use the IAM Policy Simulator, which allows us to test and validate policies without actually affecting any of the AWS resources.

### IAM Policy Simulator in Action

In the IAM Policy Simulator, we can choose what user group we want to test the policies on, in this case, we are testing the development user group.

<div align="center">

  ![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/Policy%20Sim%20Group.png)
  
</div>

<br />

We will then select the actions we want to test the group permissions against (we'll test "DeleteTags" and "StopInstances").

Then, we can simply run the simulation and check the results.

![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/Policy%20Sim%20Denied.png)

<br />

The IAM Policy Simulator also allows us to see which statements in our IAM policy are affecting the permissions on the action we tested.

![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/Policy%20Sim%20Statement.png)

<br />

The "StopInstances" action was also denied, however, if we specify the resource tags to Env - development (See the Condition statement in the IAM policy) and retest, we can see the "StopInstances" action is, in fact, allowed for the developer group (on resources tagged Env - development).

![image](https://github.com/mohammedshahwan/AWS-IAM/blob/main/Assets/Policy%20Sim%20Allowed.png)

<br />

## Conclusion

In this project, I simulated IAM controls in a development/production environment. IAM users were created and assigned to appropriate IAM user groups. A JSON policy was created and attached to the developer user group to control access and permissions to developer and production instances. After testing the policy's effectiveness, both manually and using the IAM Policy Simulator, I was able to confirm that the IAM policy was effective and allowed/denied the specified permissions as expected.

