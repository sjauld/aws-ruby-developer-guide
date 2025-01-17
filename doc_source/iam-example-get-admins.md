# Listing IAM Users who are Administrators<a name="iam-example-get-admins"></a>

The following example uses the [get\_account\_authorization\_details](https://docs.aws.amazon.com/sdkforruby/api/Aws/IAM/Client.html#get_account_authorization_details-instance_method), method to get the list of users for the current account\.

Choose `Copy` to save the code locally\.

Create the file *get\_admins\.rb*\.

Add the required IAM gem and the **os** gem, and use the latter to use the bundled certificate if you are running on Microsoft Windows\.

**Note**  
Version 2 of the AWS SDK for Ruby didn’t have service\-specific gems\.

```
require 'aws-sdk-iam'  # v2: require 'aws-sdk'
require 'os'

if OS.windows?
 Aws.use_bundled_cert!
end
```

Create a method to determine whether the user has a policy with administrator privileges\.

```
def user_has_admin_policy(user, admin_access)
  policies = user.user_policy_list

  policies.each do |p|
    if p.policy_name == admin_access
      return true
    end
  end

  false
end
```

Create a method to determine whether the user has an attached policy with administrator privileges\.

```
def user_has_attached_policy(user, admin_access)
  attached_policies = user.attached_managed_policies

  attached_policies.each do |p|
    if p.policy_name == admin_access
      return true
    end
  end

  false
end
```

Create a method to determine whether a group to which the user belongs has a policy with administrator privileges\.

```
```

Create a method to determine whether a group to which the user belongs has an attached policy with administrator privileges\.

```
def group_has_admin_policy(client, group, admin_access)
 resp = client.list_group_policies(
   group_name: group.group_name
 )

 resp.policy_names.each do |name|
   if name == admin_access
     return true
   end
 end

 false
end
```

Create a method to determine whether a group to which the user belongs has administrator privileges\.

```
def user_has_admin_from_group(client, user, admin_access)
 resp = client.list_groups_for_user(
   user_name: user.user_name
 )

 resp.groups.each do |group|
   has_admin_policy = group_has_admin_policy(client, group, admin_access)
   if has_admin_policy
     return true
   end

   has_attached_policy = group_has_attached_policy(client, group, admin_access)
   if has_attached_policy
     return true
   end
 end

 false
end
```

Create a method to determine whether the user has administrator privileges\.

```
def is_user_admin(client, user, admin_access)
 has_admin_policy = user_has_admin_policy(user, admin_access)
 if has_admin_policy
   return true
 end

 has_attached_admin_policy = user_has_attached_policy(user, admin_access)
 if has_attached_admin_policy
   return true
 end

 has_admin_from_group = user_has_admin_from_group(client, user, admin_access)
 if has_admin_from_group
   return true
 end

 false
end
```

Create a method to loop through a list of users and return how many of those users have administrator privileges\.

```
<code>
```

The main routine starts here\. Create an IAM client and variables to store the number of users, number of users who have adminstrator privileges, and the string that identifies a policy that supplies adminstrator privileges\.

```
def get_admin_count(client, users, admin_access)
 num_admins = 0

 users.each do |user|
   is_admin = is_user_admin(client, user, admin_access)
   if is_admin
     puts user.user_name
     num_admins += 1
   end
 end

 num_admins
end
```

Call `get_account_authorization_details` to get the details of the account and get the users for the account from `user_detail_list`\. Keep track of how many users we get, call `get_admin_count` to get the number of those users who have administrator privileges, and keep track of the number of those\.

```
details = client.get_account_authorization_details(
 filter: ['User']
)

users = details.user_detail_list
num_users += users.count
more_admins = get_admin_count(client, users, access_admin)
num_admins += more_admins
```

If the first call to `get_account_authorization_details` did not get all of the details, call it again and repeat the process of determining how many have administrator privileges\.

```
<code>
```

Finally, display how many users have administrator privileges\.

```
```

more\_users = details\.is\_truncated

**while more\_users**    
**details = client\.get\_account\_authorization\_details\(**  
filter: \[‘User’\], marker: details\.marker
\)  
users = details\.user\_detail\_list  
num\_users \+= users\.count more\_admins = get\_admin\_count\(client, users, access\_admin\) num\_admins \+= more\_admins  
more\_users = details\.is\_truncated

end

See the [complete example](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/ruby/iam/iam_ruby_example_show_admins.rb) on GitHub\.