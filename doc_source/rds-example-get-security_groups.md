# Getting Information about All Amazon RDS Security Groups<a name="rds-example-get-security_groups"></a>

The following example lists the names of all of your Amazon RDS security groups in the `us-west-2` region\.

**Note**  
Amazon RDS security groups are only applicable when you are using the Amazon EC2\-Classic platform\. If you are using Amazon EC2\-VPC, use VPC security groups\. Both are shown in the example\.

**Warning**  
We are retiring EC2\-Classic on August 15, 2022\. We recommend that you migrate from EC2\-Classic to a VPC\. For more information, see **Migrate from EC2\-Classic to a VPC** in the [Amazon EC2 User Guide for Linux Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-migrate.html) or the [Amazon EC2 User Guide for Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/vpc-migrate.html)\. Also see the blog post [EC2\-Classic Networking is Retiring – Here's How to Prepare](http://aws.amazon.com/blogs/aws/ec2-classic-is-retiring-heres-how-to-prepare/)\.

```
# Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
#
# This file is licensed under the Apache License, Version 2.0 (the "License").
# You may not use this file except in compliance with the License. A copy of the
# License is located at
#
# http://aws.amazon.com/apache2.0/
#
# This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS
# OF ANY KIND, either express or implied. See the License for the specific
# language governing permissions and limitations under the License.

require 'aws-sdk-rds'  # v2: require 'aws-sdk'

rds = Aws::RDS::Resource.new(region: 'us-west-2')

rds.db_instances.each do |i|
  # Show any security group IDs and descriptions
  puts 'Security Groups:'

  i.db_security_groups.each do |sg|
    puts sg.db_security_group_name
    puts '  ' + sg.db_security_group_description
    puts
  end

  # Show any VPC security group IDs and their status
  puts 'VPC Security Groups:'

  i.vpc_security_groups.each do |vsg|
    puts vsg.vpc_security_group_id
    puts '  ' + vsg.status
    puts
  end
end
```